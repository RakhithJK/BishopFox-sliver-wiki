### Setup

Use the following steps to configure a domain for DNS C2 (and DNS Canaries), you can use any DNS provider you wish as long as you setup the records correctly. I recommend setting a TTL of ~5 minutes for each record.

1. Create an `A` record for your `example.com` pointing at your Sliver server (or redirector) IP address.
2. Create an `A` record for an `ns1` subdomain (i.e. `ns1.example.com`) that points to your Sliver server (or redirector) IP address.
3. Create an NS record with an arbitrary subdomain, for example `1` (i.e. `1.example.com`) which is managed by `ns1.example.com`.
4. You can now use `1.example.com` as your DNS C2 domain e.g. `generate --dns 1.example.com`

The final configuration should look like for the domain `lil-peep.rip`:
![DNS Configuration](https://i.imgur.com/hpOnGJp.png)


__IMPORTANT:__ Remember to disable Cloudflare's "cloud" when configuring these records, and to adjust the TTLs.


### DNS Canaries

DNS Canaries are unique per-binary domains that are optionally inserted during the string obfuscation process that can be used to detect manual anlysis of Sliver binaries. These domains are not actually used by the implant code and are deliberately _not obfuscated_ so that the domains show up if someone runs `strings` on the implant. If these domains are ever resolve (and you have a `dns` listener running) you'll get an alert telling which specific file was discovered.


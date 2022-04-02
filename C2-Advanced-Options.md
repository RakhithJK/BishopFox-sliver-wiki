Advanced options, as the name suggests, are for advanced users that know what they're doing. Using these options isn't the best user experience and may result in broken or unexpected implant behavior. Only use these options if you understand what they do.

Advanced options are configured per-C2 endpoint and are passed as URL encoded parameters to the C2 URL in the `generate` command.

### HTTP C2 Advanced Options

* `net-timeout` - Network timeout value, parsed by `time.ParseDuration`
* `tls-timeout` - TLS handshake timeout value, parsed by `time.ParseDuration`
* `poll-timeout` - Poll timeout value, parsed by `time.ParseDuration`
* `max-errors` - Max number of http errors before failing (integer parsed by `strconv.Atoi`
* `driver` - Manually specify the HTTP driver (string). On windows this value can be set to `wininet` to use the `wininet` HTTP library for C2 communication.
* `force-http` - Set to `true` to always force the use of plaintext HTTP.
* `disable-accept-header` - Set to `true` to disable the HTTP accept request header.
* `disable-upgrade-header` - Set to `true` to disable the HTTP upgrade request header.
* `proxy` - Manually specify HTTP proxy, this is only value when using the Go HTTP driver, and the format should be one that is accepted by the Go HTTP library.
* `proxy-username` - Specify a proxy username. Only valid with the Go HTTP driver.
* `proxy-password` - Specify the proxy password. Only valid with the Go HTTP driver.
* `ask-proxy-creds` - Set to `true` to ask the user for HTTP proxy credentials. Only valid when used with the `wininet` HTTP driver.
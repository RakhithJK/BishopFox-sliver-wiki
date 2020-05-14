
## Server Configuration

Starting in version 1.0.0 the Sliver server has a configuration file located the `configs` sub-directory of the [`SLIVER_ROOT_APP_DIR`](https://github.com/BishopFox/sliver/wiki/Environment-Variables#assets), by default this will be `~/.sliver/configs/server.json`. If no configuration file exists, a default configuration will be generated and written to disk on startup. The default configuration is shown below:

#### Default Server Config
```
{
    "daemon_mode": false,
    "daemon": {
        "host": "",
        "port": 31337
    },
    "logs": {
        "level": 5,
        "grpc_unary_payloads": true,
        "grpc_stream_payloads": true
    }
}
```

#### Configuration Options
* `daemon_mode` - Enable [daemon mode](https://github.com/BishopFox/sliver/wiki/Daemon-Mode)
* `daemon` - An object containing options related to `daemon_mode`, these values are only used when `daemon_mode` is set to `true`.
  * `host` - What network interface to bind the `daemon_mode` client listener to. By default this is an empty string, which indicates binding to all interfaces.
  * `port` - TCP port to bind the `deamon_mode` client listener to.
* `logs` - An object containing configure options for server logs
  * `level` - The `logrus` logging level (1 - 5), default this is set to `5`, which is the`DEBUG` level.
  * `grpc_unary_payloads` - Log gRPC unary payloads
  * `grpc_stream_payloads` - Log gRPC streaming payloads
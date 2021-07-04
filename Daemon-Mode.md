Starting in v1.0.0 Sliver supports running in "daemon mode," which automatically starts a client listener (but not an interactive console). In order to connect to a server running in daemon mode you'll need to have already setup and configured [multiplayer mode](https://github.com/BishopFox/sliver/wiki/Multiplayer-Mode). 

Simply set `daemon_mode` to `true` in the `configs/server.json` located in `SLIVER_ROOT_DIR` (by default: `~/.sliver/configs/server.json`). The listener can be configured via the `daemon` object. The process will respond gracefully to `SIGTERM` on Unix-like operating systems.

#### Example Config

```
$ cat ~/.sliver/configs/server.json 
{
    "daemon_mode": true,
    "daemon": {
        "host": "",
        "port": 31337
    },
    "logs": {
        "level": 5,
        "grpc_unary_payloads": false,
        "grpc_stream_payloads": false
    }
}
``` 

#### systemd

With this config you can easily setup a [systemd service](https://www.linode.com/docs/quick-answers/linux/start-service-at-boot/) or init script. See the [Linux install script](https://github.com/BishopFox/sliver/wiki/Linux-Install-Script) for an example.
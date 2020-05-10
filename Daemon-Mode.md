Starting in v1.0.0 Sliver supports running in "daemon mode," which automatically starts a client listener (but not an interactive console). In order to connect to a server running in daemon mode you'll need to have already setup and configured [multiplayer mode](https://github.com/BishopFox/sliver/wiki/Multiplayer-Mode). 

Simply set `daemon_mode` to true in the `configs/server.json` located in `SLIVER_ROOT_DIR` (by default: `~/.sliver/configs/server.json`).

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
        "grpc_unary_payloads": true,
        "grpc_stream_payloads": true
    }
}
``` 
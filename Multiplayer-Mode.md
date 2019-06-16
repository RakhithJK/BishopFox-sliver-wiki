Multiplayer-mode allows multiple operators (players) to connect to the same Sliver server and collaborate on engagements.

## New Players (Operators)

Operators will need to download their preferred platform's client software from the releases page. (Or build from source if they like) Clients can connect to servers of differing platforms (e.g. a Windows client can connect to a Linux server). Operators and servers authenticate using Mutual TLS. The client certificates are automatically generated and managed for you using the `new-player` command.  

__NOTE__: The `new-player` and `multiplayer` commands are only accessible from the server's console.

```
sliver > new-player --operator moloch --lhost example.com

[*] Generating new client certificate, please wait ...
[*] Saved new client config to: /Users/moloch/Desktop/moloch_example.com.cfg
```

You can now give this configuration file `moloch_example.com.cfg` to the operator and they can connect to the server using the `sliver-client` binary. The sliver client will look for configuration files in `~/.sliver-client/configs/` or you can specify an exact config file with the `-config` flag. The configs directory can contain multiple configs for different servers.


```
$ ./sliver-client
? Select a server:  [Use arrows to move, type to filter]
> example.com
  localhost
```
Multiplayer-mode allows multiple operators (players) to connect to the same Sliver server and collaborate on engagements. The easiest way to setup a server for multiplayer is to use the [Linux install script](https://github.com/BishopFox/sliver/wiki/Linux-Install-Script) which will configure the server as a systemd service. However, any Sliver server binary supports multiplayer mode.

## New Players (Operators)

Operators will need to download their preferred platform's client software from the releases page. (Or build from source if they like) Clients can connect to servers of differing platforms (e.g. a Windows client can connect to a Linux server). Operators and servers authenticate using Mutual TLS. The client certificates are automatically generated and managed for you using the `new-player` command. 

* If you did not install the server as a systemd service, be sure to leave the server console running in screen or [tmux](https://github.com/tmux/tmux) after starting the multiplayer listener. 
* If you did install the server as systemd service see the CLI section below for an alternative to the console commands.

__NOTE__: The `new-player` and `multiplayer` commands are only accessible from the server's console (see below if you're running in daemon mode).

```
[server] sliver > new-player --operator moloch --lhost 1.2.3.4

[*] Generating new client certificate, please wait ...
[*] Saved new client config to: /Users/moloch/Desktop/moloch_example.com.cfg

[server] sliver > multiplayer

[*] Multiplayer mode enabled!

```

__Important:__ Before clients can connect to a server you must start an RPC listener with the `multiplayer` command. The default port is TCP/31337.

You can now give this configuration file `moloch_example.com.cfg` to the operator and they can connect to the server using the `sliver-client` binary. The sliver client will look for configuration files in `~/.sliver-client/configs/` or you can specify an exact config file with the `-config` flag. The configs directory can contain multiple configs for different servers.

```
$ ./sliver-client
? Select a server:  [Use arrows to move, type to filter]
> example.com
  localhost
```

### CLI

If the server is running in daemon mode (as is the default with the Linux install script), that means the multiplayer listener is started for you without an interactive console. You can use the server binaries' CLI to generate operator configuration files to connect:

```
./sliver-server operator --name zer0cool --lhost 1.2.3.4 --save zer0cool.cfg
```
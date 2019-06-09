Multiplayer-mode allows multiple operators (players) to connect to the same Sliver server and collaberate on engagements.

## New Players

Operators will need to download their prefer platform's client software from the releases page. Clients can connect to servers of differing platforms (e.g. a Windows client can connect to a Linux server). The operator authenticates using Mutual TLS. The client certificates are automatically generated and managed for you using the `new-player` command.  

__NOTE__: The `new-player` and `multiplayer` commands are only accessible from the server's console.

```
sliver > new-player --operator moloch --save /root/

```
## Server Setup

The server supports Linux, Windows, and MacOS however we recommend running the server on a Linux host, some features may be more difficult to get working on a Windows or MacOS server but all basic built-in functionality should work fine.

Download the latest [release](https://github.com/BishopFox/sliver/releases) for your platform, and just run the binary.

__NOTE:__ Sliver has two external dependancies for optional features: MinGW and Metasploit. To enable shellcode/staged payloads you need to install MinGW. To enable MSF integrations you'll need Metasploit installed.


## MinGW Setup

In order to enable shellcode/staged/DLL payloads you'll need to install MinGW on the server (clients connecting to the server do not need it installed). By default Sliver will look in the usual places for MinGW binaries but you can override this using the [environment variable](https://github.com/BishopFox/sliver/wiki/Environment-Variables) `SLIVER_CC`.

### Linux

```
apt-get install mingw-w64 binutils-mingw-w64 g++-mingw-w64
```

### MacOS

```
brew install mingw-w64
```

## Metasploit Setup

We strongly recommend using the [nightly framework installers](https://github.com/rapid7/metasploit-framework/wiki/Nightly-Installers), Sliver expects version 5 or later.


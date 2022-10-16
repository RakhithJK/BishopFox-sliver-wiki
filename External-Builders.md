Starting in v1.5.30 Sliver supports "external builders," which allow a Sliver server to offload implant builds onto other systems. This can be used to increase platform support (e.g. connecting a MacBook to Linux server to enable additional MacOS targets) or increasing performance (e.g. having a low powered cloud host offload a local PC).

External builders can also be used to create custom modifications to the implant source code, or replace the default Sliver implant entirely.

## Builders

#### Setup

Any `sliver-server` binary can be started as a builder process using [operator configuration files from multiplayer-mode](https://github.com/BishopFox/sliver/wiki/Multiplayer-Mode), for example:

```
./sliver-server builder -c operator-multiplayer.cfg
```

When started as a builder, the Sliver server will mirror log output to stdout by default, however this can be disabled (see `sliver-server builder --help`).

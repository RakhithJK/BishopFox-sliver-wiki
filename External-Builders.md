Starting in v1.5.30 Sliver supports "external builders," which allow a Sliver server to offload implant builds onto other systems. This can be used to increase platform support (e.g. connecting a MacBook to Linux server to enable additional MacOS targets) or increasing performance (e.g. having a low powered cloud host offload a local PC).

External builders can also be used to create custom modifications to the implant source code, or replace the default Sliver implant entirely.

## Builders

#### Setup

Any `sliver-server` binary can be started as a builder process using [operator configuration files from multiplayer-mode](https://github.com/BishopFox/sliver/wiki/Multiplayer-Mode), for example:

```
./sliver-server builder -c operator-multiplayer.cfg
```

When started as a builder, the Sliver server will mirror log output to stdout by default, however this can be disabled (see `sliver-server builder --help`).

#### External Builds

Any operator can see which builders are connected to the server using the `builders` command. This command will also show what templates, formats, and compiler targets each builder supports:

```
sliver > builders 

 Name                            Operator   Templates   Platform       Compiler Targets         
=============================== ========== =========== ============== ==========================
 molochs-MacBook-Pro-111.local   builder    sliver      darwin/arm64   EXECUTABLE:linux/386     
                                                                       EXECUTABLE:linux/amd64   
                                                                       EXECUTABLE:windows/386   
                                                                       EXECUTABLE:windows/amd64 
                                                                       EXECUTABLE:darwin/amd64  
                                                                       EXECUTABLE:darwin/arm64  
                                                                       SHARED_LIB:windows/386   
                                                                       SHARED_LIB:windows/amd64 
                                                                       SHARED_LIB:darwin/amd64  
                                                                       SHARED_LIB:darwin/arm64  
                                                                       SHARED_LIB:linux/amd64   
                                                                       SERVICE:windows/386      
                                                                       SERVICE:windows/amd64    
                                                                       SHELLCODE:windows/386    
                                                                       SHELLCODE:windows/amd64
```

Use the `--external-builder` flag to offload a `generate` or `generate beacon` command onto an external builder:

```
[server] sliver > generate --mtls localhost --os mac --arch arm64 --external-builder

[*] Using external builder: molochs-MacBook-Pro-111.local
[*] Externally generating new darwin/arm64 implant binary
[*] Symbol obfuscation is enabled
[*] Creating external build ... done
[*] Build completed in 1m19s
```

If a given format/target combination is supported by multiple external builders you will be prompted to select on for the build.

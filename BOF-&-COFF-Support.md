Sliver v1.5 and later support the loading and execution of BOFs and COFFs, generally no code changes are needed to use BOFs with a Sliver implant. However, you may need to define a manifest file so that Sliver is aware of BOF arguments and their types.

BOF support is provided via the [COFF Loader](https://github.com/sliverarmory/COFFLoader) extension, you'll need it installed to run pretty much any BOF. Typically though, the COFF Loader is just a dependency of the BOF you actually want to use and will be installed automatically. 

The easiest way to install a BOF extension, for example [`nanodump`](https://github.com/sliverarmory/nanodump), is using the [armory](https://github.com/BishopFox/sliver/wiki/Armory) package manager:

```
sliver > armory install nanodump

[*] Installing extension 'nanodump' (v0.0.5) ... done!
```

__IMPORTANT:__ Extensions (and aliases) are installed per-sliver client, they are not stored on the server. Thus extensions are not shared across operators, each operator must install the extension to use it.


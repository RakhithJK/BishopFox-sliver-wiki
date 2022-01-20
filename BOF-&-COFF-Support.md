Sliver v1.5 and later support the loading and execution of BOFs and COFFs, generally no code changes are needed to use BOFs with a Sliver implant. However, you may need to define a manifest file so that Sliver is aware of BOF arguments and their types.

### Official Extensions

BOF support is provided via the [COFF Loader](https://github.com/sliverarmory/COFFLoader) extension, you'll need it installed to run pretty much any BOF. Typically though, the COFF Loader is just a dependency of the BOF you actually want to use and will be installed automatically. 

The easiest way to install a BOF extension, for example [`nanodump`](https://github.com/sliverarmory/nanodump), is using the [armory](https://github.com/BishopFox/sliver/wiki/Armory) package manager:

__IMPORTANT:__ Extensions (and aliases) are installed per-sliver client, they are not stored on the server. Thus extensions are not shared across operators, each operator must install the extension to use it.

```
sliver > armory install nanodump

[*] Installing extension 'nanodump' (v0.0.5) ... done!

sliver > nanodump -h

A Beacon Object File that creates a minidump of the LSASS process.

Usage:
======
  nanodump [flags] pid dump-name write-file signature

Args:
=====
  pid         int       The PID of the process you want to dump.
  dump-name   string    The name of the dump file.
  write-file  int       1 = write file, 0 = fileless
  signature   string    Signature used for evasion, PMDM = default

Flags:
======
  -h, --help           display help
  -s, --save           Save output to disk
  -t, --timeout int    command timeout in seconds (default: 60)
```

### Converting Existing BOFs 


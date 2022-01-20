Sliver allows an operator to extend the local client console and its features by adding new commands based on third party tools.


## Aliases

A Sliver alias is nothing more than a folder with the following structure:

- an `alias.json` file
- alias binaries in one of the following formats:
  - .NET assemblies
  - shared libraries (`.so`, `.dll`, `.dylib`)

Here is an example for the `Rebeus` alias, reusing some of the public tools from [the GhostPack organisation](https://github.com/GhostPack):

```
$ tree GhostPack
Rubeus
├── alias.json
├── Rubeus.exe
```

The `alias.json` file has the following structure:

```json
{
    "name": "Rubeus",
    "version": "0.0.0",
    "command_name": "rubeus",
    "original_author": "@GhostPack (HarmJ0y)",
    "repo_url": "https://github.com/sliverarmory/Rubeus",
    "help": "Rubeus is a C# tool set for raw Kerberos interaction and abuses.",

    "entrypoint": "Main",
    "allow_args": true,
    "default_args": "",
    "is_reflective": false,
    "is_assembly": true,
    "files": [
        {
            "os": "windows",
            "arch": "amd64",
            "path": "Rubeus.exe"
        },
        {
            "os": "windows",
            "arch": "386",
            "path": "Rubeus.exe"
        }
    ]
}
```

It contains a single JSON object, which has the following fields:

### Alias Fields

| Field Name | Description |
| ---------- | ----------- |
| `name` | The stylized display name of the alias |
| `command_name` | The actual console command (primary identifier) |
| `entrypoint` | The entrypoint (only required for DLLs / Reflective DLLs) |
| `help` | A short help message for the command |
| `long_help` | A longer help message describing the command and its usage |
| `allow_args` | Specify whether the command will allow arguments or not |
| `files` | A list of of extension files |
| `is_reflective` | Indicates whether the extension is a reflective DLL or not |
| `is_assembly` | Indicates whether the extension is a .NET assembly or not |

#### Files

| Field Name | Description |
| ---------- | ----------- |
| `os` | The operating system for which the file can be used on (i.e., `GOOS` syntax)  |
| `arch` | The cpu architecture (i.e., `GOARCH` syntax) |
| `path` | Relative path to the file from the `alias.json`, parent directories are not allowed |

To load an alias in Sliver, use the `alias load` command:

```
sliver (CONCRETE_STEEL) > alias load /home/lesnuages/tools/misc/sliver-extensions/GhostPack

[*] Adding rubeus command: Rubeus is a C# toolset for raw Kerberos interaction and abuses.
[*] Adding seatbelt command: Seatbelt is a C# project that performs a number of security oriented host-survey "safety checks" relevant from both offensive and defensive security perspectives.
[*] Adding sharpwmi command: SharpWMI is a C# implementation of various WMI functionality.
[*] GhostPack extension has been loaded
```

The `help` command will now list the commands added by this extension:

```
sliver (CONCRETE_STEEL) > help
...
Sliver - 3rd Party extensions:
==============================
  rubeus    [GhostPack] Rubeus is a C# toolset for raw Kerberos interaction and abuses.
  seatbelt  [GhostPack] Seatbelt is a C# project that performs a number of security oriented host-survey "safety checks" relevant from both offensive and defensive security perspectives.
  sharpwmi  [GhostPack] SharpWMI is a C# implementation of various WMI functionality.
...
```

### Writing Aliases

To write a new alias, one must either create a shared library or a .NET assembly, then write a manifest file compliant with the description above.

As the alias support relies on Sliver side loading capabilities, please make sure to read the [Using 3rd party tools](https://github.com/BishopFox/sliver/wiki/Using-3rd-party-tools) section, to understand how shared libraries are loaded on all platforms.
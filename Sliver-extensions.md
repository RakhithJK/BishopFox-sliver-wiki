# Description

Sliver allows an operator to extend its features by adding new commands based on third party tools.
A Sliver extension is nothing more than a folder with the following structure:

- a `manifest.json` file
- extension binaries in one of the following formats:
  - .NET assemblies
  - shared libraries (`.so`, `.dll`, `.dylib`)

Here is an example for the `GhostPack` extension, reusing some of the public tools from [the GhostPack organisation](https://github.com/GhostPack):

```
$ tree GhostPack
GhostPack
├── manifest.json
├── Rubeus.exe
├── Seatbelt.exe
└── SharpWMI.exe
```

The `manifest.json` file has the following structure:

```json
{
    "extensionName": "GhostPack",
    "extensionCommands": [
        {
            "name": "rubeus",
            "entrypoint": "Main",
            "help": "Rubeus is a C# toolset for raw Kerberos interaction and abuses.",
            "longHelp": "[[.Bold]]Command[[.Normal]]: rubeus [command] [options]\n[[.Bold]]About:[[.Normal]] Rubeus is a C# toolset for raw Kerberos interaction and abuses.\n\nRun [[.Bold]]rubeus /help[[.Normal]] to list all examples.",
            "allowArgs": true,
            "extFiles": [
                {
                    "os": "windows",
                    "files": {
                        "x64": "Rubeus.exe",
                        "x86": "Rubeus.exe"
                    }
                }
            ],
            "isReflective": false,
            "isAssembly": true
        },
        {
            "name": "seatbelt",
            "entrypoint": "Main",
            "help": "Seatbelt is a C# project that performs a number of security oriented host-survey \"safety checks\" relevant from both offensive and defensive security perspectives.",
            "longHelp": "[[.Bold]]Command[[.Normal]]: seatbelt [command] [options]\n[[.Bold]]About:[[.Normal]] Seatbelt is a C# project that performs a number of security oriented host-survey \"safety checks\" relevant from both offensive and defensive security perspectives.\n\nRun [[.Bold]]seatbelt /help[[.Normal]] to list all examples.",
            "allowArgs": true,
            "extFiles": [
                {
                    "os": "windows",
                    "files": {
                        "x64": "Seatbelt.exe",
                        "x86": "Seatbelt.exe"
                    }
                }
            ],
            "isReflective": false,
            "isAssembly": true
        },
        {
            "name": "sharpwmi",
            "entrypoint": "Main",
            "help": "SharpWMI is a C# implementation of various WMI functionality.",
            "longHelp": "[[.Bold]]Command[[.Normal]]: sharpwmi action=[action] query=[query] computername=[computername] [options]\n[[.Bold]]About:[[.Normal]] SharpWMI is a C# implementation of various WMI functionality. This includes local/remote WMI queries, remote WMI process creation through win32_process, and remote execution of arbitrary VBS through WMI event subscriptions. Alternate credentials are also supported for remote methods.",
            "allowArgs": true,
            "extFiles": [
                {
                    "os": "windows",
                    "files": {
                        "x64": "SharpWMI.exe",
                        "x86": "SharpWMI.exe"
                    }
                }
            ],
            "isReflective": false,
            "isAssembly": true
        }
    ]
}
```
It contains a single JSON object, which has the following fields:

## Extension

| Field Name | Description |
| ---------- | ----------- |
| `extensionName` | Name of the extension |
| `extensionCommands` | List of extension commands to add to Sliver |

## Extension Command

| Field Name | Description |
| ---------- | ----------- |
| `name` | Name of the command |
| `entrypoint` | The entrypoint (only required for DLLs / Reflective DLLs) |
| `help` | A short help message for the command |
| `longHelp` | A longer help message describing the command and its usage |
| `allowArgs` | Specify whether the command will allow arguments or not |
| `extFiles` | A list of of extension files |
| `isReflective` | Indicates whether the extension is a reflective DLL or not |
| `isAssembly` | Indicates whether the extension is a .NET assembly or not |

## Extension Files

| Field Name | Description |
| ---------- | ----------- |
| `os` | The operating system for which the extension file can be used on |
| `files` | Set of `key`: `file path` linking a file path to an architecture |

To load an extension in Sliver, use the `load-extension` command:

```
sliver (CONCRETE_STEEL) > load-extension /home/lesnuages/tools/misc/sliver-extensions/GhostPack

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

# Writing new extensions

To write a new extension, one must either create a shared library or a .NET assembly, then write a manifest file compliant with the description above.
As the extension support relies on Sliver side loading capabilities, please make sure to read the [Using 3rd party tools](https://github.com/BishopFox/sliver/wiki/Using-3rd-party-tools) section, to understand how shared libraries are loaded on all platforms.
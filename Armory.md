The armory is the Sliver Alias and Extension package manager, which was introduced in Sliver v1.5 it allows you to automatically install various 3rd party tools such as BOFs and more.

## The Official Armory

The official armory ships with Sliver binaries and is included by default in the `Makefile` when compiling from source. You can interact with the Armory using the `armory` command. Packages installed from the official armory are compiled and cryptographically signed by the Sliver authors. While we make a best effort to review 3rd party code, you are responsible for reviewing and understanding any 3rd party code before using it.

#### Installing Packages

```
sliver > armory install rubeus

[*] Installing alias 'Rubeus' (v0.0.21) ... done!
```

#### Removing Packages

You remove packages installed by the `armory` using the `aliases rm` and `extensions rm` commands depending on if the package is an alias or an extension. You can list installed aliases and extensions by running `aliases` and `extensions` respectfully.

## Private Armories

Sliver also supports self-hosted private armories, but I've not gotten around to writing the details about this so you'll have to read thru the code for now.
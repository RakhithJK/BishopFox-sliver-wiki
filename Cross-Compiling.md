__NOTE:__ Any platform can cross-compile a standalone executable to any other platform out of the box, you only need cross-compilers when using `--format shared` or `--format shellcode`.


## From Linux to MacOS/Windows

1. For MacOS we recommend using https://github.com/tpoechtrager/osxcross by default Sliver will look in `/opt/osxcross` but you can override this via [environment variables](https://github.com/BishopFox/sliver/wiki/Environment-Variables)
2. Install mingw from your local package manager: `apt install mingw-w64`


## From MacOS to Linux/Windows

On MacOS `musl-cross` can be used to target 64-bit Windows, and mingw to target Windows:

```
brew install FiloSottile/musl-cross/musl-cross
brew install mingw-w64
```

I'm not aware of any good options to target 32-bit Linux from MacOS.

## From Windows to MacOS/Linux

Good luck.
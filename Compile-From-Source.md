You'll want to compile from a MacOS or Linux machine, compiling from native Windows in theory should work but none of the scripts are designed to run on Windows (you can compile the Windows binaries from MacOS or Linux). If you only have a Windows machine see "Windows Builds" below.


### Docker Build

The Docker builds are mostly designed for running unit tests, but can be useful if you want a "just do everything" build, you just need to have Docker installed on the machine. First `git clone` the Sliver repo, then run the `build.py` script (the script isn't required but has a few short cuts see `./build.py --help`). Alternatively, execute the following command: 

```
docker build -t sliver .
```

The Docker build includes mingw and Metasploit, so it can take a while to build from scratch but Docker should cache the layers effectively. Sliver will also run it's unit tests as part of the build, and that takes a few minutes too.

### From Scratch (No Docker)

From scratch without Docker, requirements for compiling:

* Go v1.14 or later
* `make`, `sed`, `tar`, `wget`, `zip` commands
* Protobuf compiler (i.e., `protoc`) 3.11 or later
  * [protoc](https://github.com/protocolbuffers/protobuf/releases)
* `protoc-gen-go` (v1.3.5, see below)
* `packr` (v1.30.1, see below)
* Clone `github.com/bishopfox/sliver`

__NOTE:__ Note that you will need `packr`, `protoc-gen-go`, and `protoc` on your `$PATH`

#### `protoc-gen-go`

To install v1.3.5 you'll need to run the following commands:

```
wget -O protoc-gen-go.tar.gz https://github.com/golang/protobuf/archive/v1.3.5.tar.gz
tar xvf protoc-gen-go.tar.gz
cd protobuf-1.3.5
make install
```

#### `packr`

To install v1.30.1 you'll need to run the following commands:

```
wget -O packr.tar.gz https://github.com/gobuffalo/packr/archive/v1.30.1.tar.gz
tar xvf packr.tar.gz
cd packr-1.30.1
make install
```

#### Go Assets

Sliver embeds its own version of the Go compiler and related tools. Run the following script to download these static assets. You need to do this at least once before running `make`:

```
$ ./go-assets.sh
```

#### Sliver

Build thin server (for development):

```
$ make
```

Statically compile and bundle server with all dependencies and assets:

```
$ make static-macos
$ make static-linux
$ make static-windows
```

__NOTE:__ Static builds do not include the optional runtime dependencies: mingw and Metasploit.

### Windows Builds

If all you have is a Windows machine, the easiest way to build Sliver is using [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10) and following the Linux instructions above. However, the `sliver-server` cannot be run directly under WSL (database limitations), so you'll want to use WSL to cross-compile a native Windows binary `make static-windows` and copy it to your Windows file system (i.e. `/mnt/c/Users/foo/Desktop`) and run it using a terminal that supports ANSI sequences such as the [Windows Terminal](https://github.com/microsoft/terminal).
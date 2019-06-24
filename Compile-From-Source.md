You'll want to compile from a MacOS or Linux machine, compiling from native Windows in theory should work but none of the scripts are designed to run on Windows (you can compile the Windows binaries from MacOS or Linux). If you only have a Windows machine see "Windows Builds" below.


### Docker Build
The Docker builds are mostly designed for running unit tests, but can be useful if you want a "just do everything" build, you just need to have Docker installed on the machine. First `git clone` the Sliver repo, then run the `build.py` script (the script isn't required but has a few short cuts see `./build.py --help`). Alternatively, execute the following command: 

```
docker build -t sliver .
```

The Docker build includes mingw and Metasploit, so it can take a while to build from scratch but Docker should cache the layers effectively. Sliver will also run it's unit tests as part of the build, and that takes a few minutes too.

### From Scratch (No Docker)
From scratch without Docker, requirements for compiling:

* Go v1.11 or later
* `make`, `sed`, `tar`, `wget`, `zip` commands
* [dep](https://golang.github.io/dep/) 
  * Linux `curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh`
  * MacOS `brew install dep`
* [Protobuf 3.7 or later](https://github.com/golang/protobuf) You need both `protoc` and `protoc-gen-go`
  * [protoc](https://github.com/protocolbuffers/protobuf/releases)
  * `go get -u github.com/golang/protobuf/protoc-gen-go`
* packr (v1) `go get -u github.com/gobuffalo/packr/packr`
* Clone the project into `$GOPATH/src/github.com/bishopfox/sliver`

__NOTE:__ Note that you need `$GOPATH/bin` (i.e. `packr`, `protoc-gen-go`) on your `$PATH` as well as `protoc`

Build thin server (for development):

```
$ dep ensure
$ ./go-assets.sh
$ make
```

Statically compile and bundle server with all dependencies and assets:

```
$ dep ensure
$ ./go-assets.sh
$ make static-macos
$ make static-linux
$ make static-windows
```

### Windows Builds

If all you have is a Windows machine, the easiest way to build Sliver is using [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10) and following the Linux instructions above. However, the `sliver-server` cannot be run directly under WSL (database limitations), so you'll want to use WSL to cross-compile a native Windows binary `make static-windows` and copy it to your Windows file system (i.e. `/mnt/c/Users/foo/Desktop`) and run it using a terminal that supports ANSI sequences such as the [Windows Terminal](https://github.com/microsoft/terminal).
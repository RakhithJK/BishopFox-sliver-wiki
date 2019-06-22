You'll want to compile from a MacOS or Linux machine, compiling from Windows should work but none of the scripts are designed to run on Windows (you can compile the Windows binaries from MacOS or Linux). If all you have is a Windows machine, using the Docker build will probably the easiest choice.

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
* [Protobuf](https://github.com/golang/protobuf) You need both `protoc` and `protoc-gen-go` on your `$PATH`
  * [protoc](https://github.com/protocolbuffers/protobuf/releases)
  * `go get -u github.com/golang/protobuf/protoc-gen-go`
* packr (v1) `go get -u github.com/gobuffalo/packr/packr`
* Clone the project into `$GOPATH/src/github.com/bishopfox/sliver`

__NOTE:__ Check that you `$GOPATH/bin` is on your `$PATH`

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

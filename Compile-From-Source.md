You'll want to compile from a MacOS or Linux machine, compiling from Windows should work but none of the scripts are designed to run on Windows (you can compile the Windows binaries from MacOS or Linux).

### Docker Build
The Docker builds are mostly designed for running unit tests, but can be useful if you want a "just do everything" build. First `git clone` the Sliver repo, then run the `build.py` script, you'll also need to have Docker installed on the machine. The Docker build includes mingw and Metasploit, so it can take a while to build from scratch but Docker should cache the layers effectively. Sliver will also run it's unit tests as part of the build, and that takes a few minutes too.

### From Scratch (No Docker)
From scratch without Docker, requirements for compiling:

* Go v1.11 or later
* `make`, `sed`, `tar`, `wget`, `zip` commands
* [dep](https://golang.github.io/dep/)
* [protoc](https://github.com/golang/protobuf)
* packr (v1) `go get -u github.com/gobuffalo/packr/packr`
* Clone the project into `$GOPATH/src/github.com/bishopfox/sliver`

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

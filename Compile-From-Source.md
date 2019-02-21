You'll want to compile from a MacOS or Linux machine, compiling from Windows should work but none of the scripts are designed to run on Windows (you can compile the Windows binaries from MacOS or Linux).

Requirements for Compiling:
* Go v1.11 or later
* `make`, `sed`, `tar`, `wget`, `zip` commands
* [dep](https://golang.github.io/dep/)
* [protoc](https://github.com/golang/protobuf)
* packr (v1) `go get -u github.com/gobuffalo/packr/packr`
* Clone the project into `$GOPATH/src/`

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

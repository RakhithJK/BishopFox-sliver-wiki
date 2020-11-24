Apple Silicon support is still in its infancy, so expect some things to be broken. We'll have much better support once Go officially supports the Apple Silicon/M1 in Go 1.16, until then here are some notes on how I was able to get Sliver to build on Apple Silicon: 

### Cross-compile Go

Modified [instructions from this thread](https://github.com/golang/go/issues/38485#issuecomment-730247329):

 * You should have access to a different architecture, preferably Apple's Intel Mac.
 * On that Mac you should already have Go installed, I had version 1.15.5 installed via homebrew.
 * Git [clone the source](https://github.com/golang/go) of Golang and `cd` into the `src` folder. You should be in the master branch.
 * Build the binaries for ARM Darwin arch: `GOARCH=arm64 GOOS=darwin ./bootstrap.bash`.
 * Copy the `go-darwin-arm64-bootstrap.tbz` to your Apple Silicon Mac, and unwrap it into `/opt/go/` folder (or anywhere you want really)
 * Add `/opt/go/bin` to your `$PATH`.
 * Sign every binary in paths `/opt/go/bin/` and `/opt/go/pkg/tool/` with `codesign -s - <binary_path>` and remove the quarantine attribute with `xattr -r -d com.apple.quarantine <binary_path>`. 
 * Add `~/go/bin` to your `$PATH`, you may need to create a `.zshrc`


### Compile Protobuf Compiler

* Download the latest [protoc source code](https://github.com/protocolbuffers/protobuf/releases) release.
* Compile:

```
./configure
make
make check
sudo make install
```

* Make sure `protoc` to your $PATH (you may have to create a `.zshrc`)

### Compile `protoc-gen-go`

* Download `https://github.com/golang/protobuf/archive/v1.3.5.tar.gz` as `protoc-gen-go.tar.gz`

```
tar xvf protoc-gen-go.tar.gz
cd protobuf-1.3.5
make install
```

* Sign `protoc-gen-go` default: `codesign -s - ~/go/bin/protoc-gen-go`

### Compile `packr`

Download `https://github.com/gobuffalo/packr/archive/v1.30.1.tar.gz` as `packr.tar.gz`:

```
tar xvf packr.tar.gz
cd packr-1.30.1
make install
```

* Sign `packr` default: `codesign -s - ~/go/bin/packr`

### Compile Sliver

* Git clone the `master` branch
* Double check that `~/go/bin` and `protoc` are your `$PATH`
* Manually put a copy of the cross-compiled `go` tool chain into `assets` (skip `go-assets.sh`), make sure it's the signed/un-quarantine'd version
* Run `make static-macos`
* Sign server `code-sign -s - sliver-server` and client `code-sign -s - sliver-client`
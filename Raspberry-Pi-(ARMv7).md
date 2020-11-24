Sliver can be compiled on Raspberry Pi/Linux:


#### Cross-compile Go

* First you'll need to cross-compile Go from another system (likely Intel):

```
GOARCH=arm GOARM=7 GOOS=linux ./bootstrap.bash
```

* Copy the `go-linux-arm-bootstrap.tbz` onto your Raspberry Pi
* Extract to `/opt/go` and add `/opt/go/bin` to your `$PATH`

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

### Compile `packr`

Download `https://github.com/gobuffalo/packr/archive/v1.30.1.tar.gz` as `packr.tar.gz`:

```
tar xvf packr.tar.gz
cd packr-1.30.1
make install
```


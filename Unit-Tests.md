The easiest way to execute the unit tests is via [Docker](https://www.docker.com/get-started), which you will need to install.

Once you have Docker installed, just execute the `run-tests.sh` script:

```
$ ./run-tests.sh

[*] Go grab some coffee, this takes a few minutes ...

Sending build context to Docker daemon  8.995MB
Step 1/18 : FROM golang:1.12
 ---> c4f8e4c91496
Step 2/18 : ENV PROTOC_VER 3.6.1
 ---> Using cache
 ---> e5902b1b2f87
Step 3/18 : RUN apt-get update &&     apt-get -y install wget git zip unzip build-essential
 ---> Using cache
 ---> f0bd4caac2e0
Step 4/18 : WORKDIR /tmp
 ---> Using cache
 ---> 3880338ec5af
Step 5/18 : RUN wget https://github.com/protocolbuffers/protobuf/releases/download/v${PROTOC_VER}/protoc-${PROTOC_VER}-linux-x86_64.zip     && unzip protoc-${PROTOC_VER}-linux-x86_64.zip     && cp -vv ./bin/protoc /usr/local/bin
 ---> Using cache
 ---> f02c81f15341
Step 6/18 : RUN go get github.com/golang/protobuf/protoc-gen-go
 ---> Using cache
 ---> dd731d768cb3
Step 7/18 : RUN go get -u github.com/gobuffalo/packr/packr
 ---> Using cache
 ---> b2d68c4e4e8f
Step 8/18 : RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
 ---> Using cache
 ---> babdb2f19066
Step 9/18 : WORKDIR /go/src/sliver
 ---> Using cache
 ---> e6e617f4bf89
Step 10/18 : ADD ./go-assets.sh /go/src/sliver/go-assets.sh
 ---> Using cache
 ---> 4820726a4465
Step 11/18 : RUN ./go-assets.sh
 ---> Using cache
 ---> 92c461ecc8d7
Step 12/18 : ADD ./Gopkg.lock /go/src/sliver/Gopkg.lock
 ---> Using cache
 ---> 7577f746fa57
Step 13/18 : ADD ./Gopkg.toml /go/src/sliver/Gopkg.toml
 ---> Using cache
 ---> c62315990ebd
Step 14/18 : RUN dep ensure --vendor-only
 ---> Using cache
 ---> f326f0b60640
Step 15/18 : ADD . /go/src/sliver/
 ---> fd5533c2a85c
Step 16/18 : RUN make static-linux
 ---> Running in deff8c55909c
packr clean
rm -f ./protobuf/client/*.pb.go
rm -f ./protobuf/sliver/*.pb.go
rm -f sliver-client sliver-server *.exe
go install ./vendor/github.com/golang/protobuf/protoc-gen-go
protoc -I protobuf/ protobuf/sliver/sliver.proto --go_out=protobuf/
protoc -I protobuf/ protobuf/client/client.proto --go_out=protobuf/
cd ./server/
packr
cd ..
sed -i '/.darwin\/go\.zip/d' ./server/assets/a_assets-packr.go
sed -i '/.windows\/go\.zip/d' ./server/assets/a_assets-packr.go
GOOS=linux CGO_ENABLED=0 go build -tags netgo -ldflags '-s -w' -o sliver-server ./server
Removing intermediate container deff8c55909c
 ---> 8d8943c6c8a9
Step 17/18 : RUN /go/src/sliver/sliver-server -unpack
 ---> Running in 43e50038d5fe
[*] First time setup, please wait ...
Removing intermediate container 43e50038d5fe
 ---> 258b847f3279
Step 18/18 : ENTRYPOINT [ "/go/src/sliver/go-tests.sh" ]
 ---> Running in 89e156279b68
Removing intermediate container 89e156279b68
 ---> ad42b7ec8d5b
Successfully built ad42b7ec8d5b
Successfully tagged sliver-tests:latest
ok  	sliver/server/certs	0.995s
ok  	sliver/server/gogo	1.844s
ok  	sliver/server/generate	223.607s
```
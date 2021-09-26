By default when you build an implant for the first time Sliver will attempt to pull down the implant's Golang dependencies from the internet. However, the server will cache Go modules it downloads, so if you can compile one implant ahead of time you should be able to compile implants offline later (assuming you do not delete the `SLIVER_ROOT_DIR` directory (by default `~/.sliver`).

### GOPROXY

As of v1.5.0 Sliver also supports the Golang `GOPROXY` environment variable, which can be configured to point to a local Go Module Proxy that contains the implant's dependencies. A `GOPROXY` can also be configured in the server's configuration file, which will take precedence over any environment variable. 

For example, you can use the [Athens](https://docs.gomods.io/) proxy to cache modules locally:

```
$ docker run -p '3000:3000' gomods/athens:latest
$ export GOPROXY=http://localhost:3000
$ ./sliver-server

[server] sliver > generate --http localhost
```

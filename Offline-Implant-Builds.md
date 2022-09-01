# Versions 1.5.21+

As of v1.5.21 offline implants builds are fully supported by Sliver, and enabled by default.

# Versions <= 1.5.20

By default when you build an implant for the first time Sliver will attempt to pull down the implant's Golang dependencies from the internet.

Due nuances in the build process offline builds are not simple, though we're always looking at ways we can implement this "out of the box" in the future. A solution is outlined below, however the simplest solution is to (if you can) generate implants ahead of time and use `regenerate` once offline, though this will require you have advanced knowledge of callback addresses.

### GOPROXY

As of v1.5.0 Sliver also supports the Golang `GOPROXY` environment variable, which can be configured to point to a local Go Module Proxy that contains the implant's dependencies. A `GOPROXY` can also be configured in the server's configuration file, which will take precedence over any environment variable. Note that the dependencies required by the implant will vary depending on the flags used to generate it (we remove unused code prior to compilation), so ensure that you use similar flags when generating offline.

For example, you can use the [Athens](https://docs.gomods.io/) proxy to cache modules locally:

```
$ docker run -d -p '3000:3000' gomods/athens:latest
$ export GOPROXY=http://localhost:3000
$ ./sliver-server

[server] sliver > generate --http localhost
```

### Athens Proxy Script

Script to start Athens:

```
export ATHENS_STORAGE=$HOME/.athens-storage
mkdir -p $ATHENS_STORAGE
docker run -d -v $ATHENS_STORAGE:/var/lib/athens \
   -e ATHENS_DISK_STORAGE_ROOT=/var/lib/athens \
   -e ATHENS_STORAGE_TYPE=disk \
   --name athens-proxy \
   --restart always \
   -p 3000:3000 \
   gomods/athens:latest
```

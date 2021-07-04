This script will install the latest version of Sliver as a systemd service, install Windows cross-compiler dependencies (mingw), and setup multiplayer. After running the script connect locally by running `sliver`

https://gist.github.com/moloch--/43fd12a704f47deaebbedab2f5be15ba

### One Liner

```bash
curl https://gist.githubusercontent.com/moloch--/43fd12a704f47deaebbedab2f5be15ba/raw/8a3749e003a8b364c7454a9c059bacc0b2173b98/install-sliver.sh | /bin/bash
```

* Installs server binary to `/root/sliver-server`
* Installs mingw
* Runs the server in daemon mode using systemd
* Installs client to `/usr/local/bin/sliver`
* Generates multiplayer configuration for current user

### Systemd Service

The following systemd configuration is used:

```
[Unit]
Description=Sliver
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=on-failure
RestartSec=3
User=root
ExecStart=/root/sliver-server daemon

[Install]
WantedBy=multi-user.target
```
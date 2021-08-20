This script will install the latest version of Sliver as a systemd service, installs Windows cross-compiler dependencies (mingw), and setup multiplayer for the all local users. After running the script connect locally by running `sliver`.

https://github.com/BishopFox/sliver/blob/master/docs/install

This script should work on Kali, Ubuntu, and RHEL (CentOS, etc) distributions of Linux.

### One Liner

```bash
curl https://sliver.sh/install|sudo bash
```

* Installs server binary to `/root/sliver-server`
* Installs mingw
* Runs the server in daemon mode using systemd
* Installs client to `/usr/local/bin/sliver`
* Generates multiplayer configurations for all users with a `/home` directory

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
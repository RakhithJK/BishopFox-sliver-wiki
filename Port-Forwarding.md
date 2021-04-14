Sliver provides two mechanisms for port forwarding to tunnel additional connections / tools into the target environment via an implant:
* `portfwd` - This command (available on all C2s) uses Sliver's in-band tunnels to transfer data between your local machine and the implant network (i.e., if you're using HTTP C2 all port forwarded traffic is tunneled over HTTP, same for mTLS/DNS/etc.)
* `wg-portfwd` - This command uses WireGuard port forwarding, and is only available when using WireGuard C2.


__NOTE:__ Generally speaking `wg-portfwd` is faster and more reliable, we recommend using it whenever possible. Some protocols may be unstable, or may not work when tunneled via `portfwd`. However, `wg-portfwd` requires a little extra setup (see below).


## Tunneled Port Forwarding


## WireGuard Port Forwarding





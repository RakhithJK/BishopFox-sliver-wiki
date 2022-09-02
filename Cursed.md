Cursed Chrome/electron post-exploitation tool kit introduced in Sliver v`TBD`, which integrates with [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome). It can automatically find existing Chrome Extensions with the required permissions for [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome) and remotely inject it onto the target system.

## Cursed Chrome

The `cursed chrome` command can be used to restart a remote system's Chrome browser with remote debugging enabled. If no payload is specified using `--payload` the command will simply restart Chrome with remote debugging enabled, you can then use `cursed console` to interact with any debug target. 

If a payload is specified, the command will restart Chrome with remote debugging, enumerate installed browser extensions, determine if any extension has the required permissions for [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome) and inject the payload into the extension's execution context.

## Cursed Console

The `cursed console` command can be used to start an interactive REPL with any cursed process. You will need to start a cursed process using `cursed chrome` or `cursed electron` before using `cursed console`.
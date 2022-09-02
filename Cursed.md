Cursed Chrome/Chromium/Edge/Electron post-exploitation tool kit introduced in Sliver v`TBD`, which integrates with [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome). It can automatically find existing Chrome Extensions with the required permissions for [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome) and remotely inject it onto the target system, or you can start an interactive REPL to inject arbitrary code into any Chrome/Chromium/Edge/Electron context.

## Cursed Chrome

The `cursed chrome` command can be used to restart a remote system's Chrome browser with remote debugging enabled. If no payload is specified using `--payload` the command will simply restart Chrome with remote debugging enabled, you can then use `cursed console` to interact with any debug target. 

If a payload is specified, the command will restart Chrome with remote debugging, enumerate installed browser extensions, determine if any extension has the required permissions for [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome), and inject the payload into the extension's execution context.

## Cursed Edge

Works identically to `cursed chrome` but the UI displays "Edge" instead of "Chrome" much like Edge itself.

## Cursed Electron

The `cursed electron` command can be used to restart an Electron application with remote debugging enabled, you can subsequently use `cursed console` to interact with any debug target. Note that some Electron applications disable the remote debugging functionality, which will prevent this feature from functioning.

## Cursed Console

The `cursed console` command can be used to start an interactive REPL with any cursed process. You will need to start a cursed process using `cursed chrome` or `cursed electron` before using `cursed console`. You can list cursed processes using the `cursed` command.

<img width="1411" alt="Screen Shot 2022-09-02 at 4 31 44 PM" src="https://user-images.githubusercontent.com/875022/188246398-97e5c7dd-1c21-4aeb-a57e-222fda826e66.png">


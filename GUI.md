⚠️ The GUI is still under active development, see the `gui` branch.

Electron based Sliver GUI written in Angular/TypeScript, for an overview of the GUI architecture and security considerations please read [Reasonably Secure Electron](https://know.bishopfox.com/research/reasonably-secure-electron).

## Build

From the `gui/` directory:

```bash
npm install
npm install -g electron-packager
npm install -g ts-protoc-gen
npm install -g @angular/cli
./protobuf.sh
npm run electron:local
```

## Source Code

Source code is organized as follows:

`main.ts` - Electron entrypoint.

`preload.js` - Electron preload script used to bridge the sandbox code to the Node process.

`ipc/` - Node IPC handler code, this translates messages from the `preload.js` script into RPC or local 
procedure calls that cannot be done from within the sandbox.

`rpc/` - TypeScript implementation of Sliver's RPC protocol.

`src/` - Angular source code (webview code).

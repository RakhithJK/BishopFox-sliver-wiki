This page documents the Sliver RPC API, which is used to communicate between the client and the server, as well as between the server and the implant (sliver).

## Client-to-Server RPC

The client-to-server RPC communication is done over mutual TLS (mTLS), since niether protobuf nor TCP provide a mechanism to denote the start and end of a message length prefix encoding is used to denote the length of the main message. This is implemented by sending a 4-byte uint32 little endian length prepended the actual message:

`[uint32 little endian length | Envelope ...]`

For example, when the connection is establish both sides start by reading 4 bytes, which indicates an n length. Next n bytes are read into a buffer, once complete the buffer is parsed as an `Evelope` protobuf message.

### Envelope

All messages are wrapped in an Envelope protobuf data structure, which describes the message and contiains some other metadata about the message.

```protobuf
message Envelope {
  uint32 Type = 1;
  uint64 ID = 2;
  int64 Timeout = 3;
  
  bytes Data = 9;

  string Err = 4;
}
```

There are currently two client RPC implementations written in Go (console client) `client/transport/` and TypeScript/JavaScript (GUI) `gui/rpc/`.

## Server-to-Implant RPC





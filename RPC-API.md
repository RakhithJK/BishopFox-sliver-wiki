This page documents the protocol used to communicate between the server and the implant (sliver), not that this protocol may go across various `transports` like HTTP, HTTPS, mTLS, or DNS.

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

* __Type__ - Type is a magic constant that tells the recipient how the message should be parsed. Note that Type values are globally unique, between both `clientpb` and `sliverpb`.
* __ID__ - Optional random numeric value used to correlate request and responses.
* __Timeout__ - Nanoseconds timeout.
* __Data__ - Protobuf message content.
* __Err__ - Optional response value that contains error information.






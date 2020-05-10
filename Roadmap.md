(Unsorted 4/9/20)

## Roadmap

* ☑ gRPC for Client to Server communication
* Better documentation for code, deployment, and usage (_in progress_)
* CI/CD pipeline
* C2 Improvements (_in progress_)
  * DNS code cleanup/refactor
  * DNS case-sensitive encoder fingerprint/detection
  * Improve HTTP(S) payload detection & randomization
  * Additional HTTP(S) encoding methods
  * Load balance domain support DNS, HTTP(S)
  * Cross-protocol C2 support (e.g. DNS+HTTPS)
* GUI (_on hold_)
* Infrastructure automation
  * DNS record management 
  * Pivot generation (API Gateway, etc)
* Better support for asynchronous commands
* Staging (Eggs)
  * Separate the listener / stager logic in two distinct RPC commands
* Agent --> Server RPC requests (agent triggered sliver generation)
* MacOS
  * Mach-O loader
* Windows
  * Process injection profiles
  * Enhance evasion techniques (PPID/Args spoofing, protect, etc.)

## Backlog

* Remote builders to avoid tricky cross-compiler targets 
* 3rd party C2 channels (Slack, Teams, S3, etc.)
* Fix SMB named pipe pivots
* Better scripting support (agent and server)
* Seamless process migration on Windows

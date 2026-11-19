---
name: "grpcurl"
categories:
  - corpus-recon
homepage: "https://github.com/fullstorydev/grpcurl"
origin: "corpus"
github-repo: "fullstorydev/grpcurl"
packaged-by: [aur, nixpkgs]
meta-checked: "2026-11-09"
github-stars: 12876
github-pushed: "2026-11-02"
github-archived: false
github-issues: 122
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 22
gh-activity-checked: "2026-11-09"
---

# grpcurl

> curl for gRPC: list, describe and call RPC methods on a gRPC server from the shell.

## What it's for

grpcurl speaks gRPC's binary protobuf protocol so you don't have to: against
a server that supports reflection it enumerates the available services,
methods and message schemas, and it invokes RPCs with JSON (or protobuf
text-format) request bodies, printing the decoded responses. For servers
without reflection you supply `.proto` sources or compiled protoset files
instead. It handles TLS, plaintext (`-plaintext`), custom headers/metadata
(`-H`) and client certificates, and supports streaming methods — including
bidirectional streaming interactively from a terminal. It is the standard
first probe when an open port turns out to speak gRPC and you need to know
what it exposes before touching a real client.

## Typical use cases

### List the services a server exposes

```
grpcurl -plaintext 198.51.100.10:50051 list
```

Outcome: prints every service name the reflection service advertises (e.g.
`simpleapp.SimpleApp`) — the map of what can be called on that endpoint.

### List one service's methods

```
grpcurl -plaintext 198.51.100.10:50051 list simpleapp.SimpleApp
```

Outcome: prints the RPC methods of that single service — the callable
surface, narrowing what to describe and invoke next.

### Describe a symbol's schema

```
grpcurl -plaintext 198.51.100.10:50051 describe simpleapp.LoginRequest
```

Outcome: prints the equivalent proto source for any service, method or
message type the server knows about — field names, types and numbers you
need to build a valid request.

### Invoke an RPC with a JSON body

```
grpcurl -plaintext -d '{"username": "user", "password": "Password1"}' \
    198.51.100.10:50051 simpleapp.SimpleApp/LoginUser
```

Outcome: sends the JSON as a protobuf-encoded LoginUser request and prints
the server's decoded response; with `-format text` the `-d` payload switches
to protobuf text format (`username: "user"`).

### Read the request body from stdin

```
echo '{"id": "123"}' | grpcurl -plaintext -d @ 198.51.100.10:50051 simpleapp.SimpleApp/GetInfo
```

Outcome: `-d @` reads the request from stdin, so `jq` or a here-doc can
build the payload; the response is printed as JSON to stdout.

### Call a server that has no reflection

```
grpcurl -plaintext -import-path protos -proto service.proto \
    -d '{"id": "123"}' 198.51.100.10:50051 simpleapp.SimpleApp/GetInfo
```

Outcome: uses the local `.proto` source (or a `-protoset` file compiled with
`protoc --descriptor_set_out`) as the schema, so list/describe/invoke work
even when the server does not run the reflection service.

### Attach metadata headers to a call

```
grpcurl -plaintext -H 'authorization: Bearer TOKEN' -H 'x-api-key: demo' \
    -d '{}' 198.51.100.10:50051 simpleapp.SimpleApp/GetInfo
```

Outcome: the request carries the given gRPC metadata (repeatable `-H
name:value`), letting authenticated or API-gated methods accept the call.

*Corpus evidence: command lines in the corpus index cover `list`, `list
<service>`, `describe <symbol>` and `-format text -d` invocations against a
plaintext reflection-enabled service; `-d @`, `-H` and the
`-proto`/`-import-path` no-reflection forms are verified against the
fullstorydev/grpcurl README. All flags above cross-checked against that
source.*

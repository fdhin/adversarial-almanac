---
name: "queensonos-install"
categories:
  - networking
---

# Installing queensonos

Prebuilt static binaries from GitHub releases (Linux):

```bash
curl -lO -L https://github.com/ariary/QueenSono/releases/latest/download/qsreceiver
curl -lO -L https://github.com/ariary/QueenSono/releases/latest/download/qssender
```

Or with Go (`$GOPATH/bin` must be in `$PATH`; rename the built commands):

```bash
go install github.com/ariary/QueenSono/cmd/client@latest
mv $GOPATH/bin/client $GOPATH/bin/qssender
go install github.com/ariary/QueenSono/cmd/server@latest
mv $GOPATH/bin/server $GOPATH/bin/qsreceiver
```

Or from a source clone: `make before.build`, then `build.queensono-sender` / `build.queensono-receiver`. Linux only (golang net/icmp); both binaries need raw-socket capability — run as root or grant `cap_net_raw`.

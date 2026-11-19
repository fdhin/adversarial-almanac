---
tool: "reverse-ssh"
build-system: "go"
upstream: "https://github.com/Fahrj/reverse-ssh"
recipe: "upstream"
license: "GPL3"
---

# Installing reverse-ssh

> Static SSH server with reverse connectivity — interactive bind/reverse shell plus sftp and forwarding.

Source: hacktricks feed; upstream [`Fahrj/reverse-ssh`](https://github.com/Fahrj/reverse-ssh).

## Prebuilt binaries (recommended)

Download the latest static binaries (multiple OS/arch combos) from the [Releases](https://github.com/Fahrj/reverse-ssh/releases/latest) page — no runtime dependencies beyond a minimal Linux 2.6.23+ / Windows 7+ userland.

## Build from source

Requires Go 1.15+; `upx` is optional for compression:

```bash
make
```

`make compressed` additionally packs the binaries with upx. Build-time environment variables customize the result: `RS_SHELL`, `RS_PASS`, `RS_PUB`, `LUSER`, `LHOST` (makes reverse mode the default), `LPORT`, `BPORT` (0 = any free port), `NOCLI`.

Cross-compile for other platforms with the usual Go variables, e.g.:

```bash
GOARCH=arm64 GOOS=linux make compressed
```

Enumerate targets with `go tool dist list`.

## Convenience

The repo ships a matching private key (`assets/id_reverse-ssh`) and an example `~/.ssh/config` Host block so `ssh target` / `sftp target` just work against the default callback port 8888. Default password is `letmeinbrudipls` — compile your own credentials in for anything real.

Backlinks: [[tunnel]] · [[reverse-ssh]]

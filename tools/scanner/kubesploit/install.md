---
tool: "kubesploit"
build-system: "go"
upstream: "https://github.com/cyberark/kubesploit"
recipe: "blackarch"
source: "https://github.com/cyberark/kubesploit"
license: "GPL3"
blackarch-pkgver: "105.ab2a57a"
---

# Installing kubesploit

> Cross-platform post-exploitation HTTP/2 Command & Control server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kubesploit) (version `105.ab2a57a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S kubesploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/cyberark/kubesploit/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/cyberark/kubesploit) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `wget`

## Exact build steps from the recipe

```bash
cd $pkgname

  wget -c 'https://go.dev/dl/go1.14.linux-amd64.tar.gz' \
    -O go1.14.linux-amd64.tar.gz

  echo 08df79b46b0adf498ea9f320a0f23d6ec59e9003660b4c9c1ce8e5e2c6f823ca \
    go1.14.linux-amd64.tar.gz | sha256sum -c

  tar -zxf go1.14.linux-amd64.tar.gz

  export GOPATH=${PWD}/go/bin
  export PATH=$GOPATH:$PATH

  go build -o "$pkgname-agent" cmd/merlinagent/main.go
  go build -o "$pkgname-server" cmd/merlinserver/main.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[kubesploit]] · [[BlackArch Tools]]


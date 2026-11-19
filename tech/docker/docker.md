---
technology: "Docker"
kind: overview
audience: offensive-security
layer: container-runtime
family: containers
homepage: "https://www.docker.com/"
github-repo: "https://github.com/moby/moby"
provenance: "corpus: 30/461 writeups, strict target-side command-context re-gate (gate + adjudication in internal coverage ledger; marker estimate was 13, bare-word 83) + upstream: Docker security announcements CVE-2025-9074 (Docker Desktop 4.44.3), Moby GHSA-3fwx-pjgw-3558 CVE-2021-41091 (<20.10.9), runc advisories CVE-2019-5736 (<=1.0-rc6 / Docker <18.09.2) and CVE-2024-21626 (<=1.1.11, fixed 1.1.12), docs.docker.com rootless-mode (socket /run/user/$UID/docker.sock). Pass 1 wave 5, 2026-11-12."
---

# Docker

> The daemon runs as root and mounts filesystems on request — anyone who can say one word to `/var/run/docker.sock` is root. 30 observed engagements ended with that sentence.

## What it is

Docker (the engine is the open-source Moby project; Docker Inc. ships Docker Engine and Docker Desktop on top of it) is the dominant Linux container runtime: a client–daemon system where `dockerd` — a **root process** — receives declarative JSON ("create a container from this image, bind-mount this host path, grant these capabilities") and does the privileged work itself: namespaces, cgroups, mounts, device nodes. The CLI is a thin client; the daemon is the privilege. Under the hood `dockerd` delegates container lifecycle to `containerd` and `runc` — the same OCI runtime Kubernetes uses — so runc/containerd CVEs land on Docker hosts too.

The design decision that matters to an attacker: the API socket `/var/run/docker.sock` is owned `root:docker` mode 660, and the daemon accepts **bind mounts of arbitrary host paths** and **arbitrary Linux capabilities** with no path filtering of its own. "Can you write to the socket" and "can you create a container" are therefore the same question as "are you root on the host". The counter-model exists — **rootless mode** (`dockerd-rootless.sh`, stable since 20.10) runs daemon and containers as an unprivileged user with the socket at `/run/user/$UID/docker.sock` — and it changes what a socket win is worth, so the first thing to establish on any Docker host is which model you are looking at.

Images are the other half of the attack surface. A registry speaking the HTTP v2 API (port 5000 by default), a downloadable image tarball, or an image layered into a web download are all **offline evidence**: environment variables, deleted secrets in earlier layers, embedded credentials survive in layers even after the application "removes" them.

## The anatomy that matters on an engagement

The chain every finding lives on:

```
docker CLI / curl / UI (Portainer) / CI runner        <- stolen client certs, injected build args
        |  unix socket /var/run/docker.sock  (or tcp 2375 plaintext, tcp 2376 TLS)
        v
dockerd (root)  -- accepts Image, Cmd, Binds, Privileged, CapAdd
        |                                  <- mount /:/mnt, --privileged, image with entrypoint
        v
containerd -> runc  (OCI runtime)          <- CVE-2019-5736, CVE-2024-21626
        |
        v
container (namespaces, dropped caps)  ---- /var/lib/docker/overlay2 (host-side)
        |  docker0 bridge 172.17.0.x       <- gateway = the host; sibling containers one hop away
        v
host kernel
```

Three structural properties drive most of the findings in the field:

1. **The socket is the boundary, not the container.** Membership in the `docker` group, a writable nonstandard socket path (`/app/docker.sock`), an env var `DOCKER_HOST`, a mounted socket inside a container, a TLS client cert in `~/.docker/`, a Portainer UI, a CI runner with a docker executor — all are equivalent to root on the daemon's host (unless the daemon is rootless).
2. **Bind mounts have no path validation.** `docker run -v /:/mnt` is the single most repeated exploit command in the observed record (seven separate engagements); the raw-HTTP form `POST /containers/create` with `"Binds":["/:/mnt:rw"]` does the same with nothing but curl (two more).
3. **Images are filesystems you can take home.** `v2/_catalog` → pull → `docker run` (or `dive` the tarball offline) reliably yields secrets the running app no longer shows — deleted databases, `ENV` credentials, source with flaws fixed in production.

## Where you'll meet it

- **App hosting** — the web shell lands *inside* a container; the engagement question becomes escape (privileged flag, capabilities, mounted host dirs, runc version) or lateral movement across the docker bridge. Thinnest coverage: capability-based escapes (`CAP_SYS_MODULE`, `CAP_DAC_READ_SEARCH`) that don't use the socket at all.
- **CI/CD** — GitLab runners with docker executors spawn job containers that inherit the runner's environment (field-observed); `docker build` reachable through Portainer or sudo turns a Dockerfile into host filesystem writes (two observed chains).
- **Private registries** — port 5000 on an internal docker subnet (172.16-31.x.x ranges) serving `/v2/_catalog` with basic auth, bearer tokens, or nothing (four observed engagements).
- **Docker Desktop** (dev machines, Windows/macOS hosts) — the Engine API has its own internal subnet address (192.168.65.x) reachable *from any container* on versions < 4.44.3 (CVE-2025-9074); Windows drives appear under `/run/desktop/mnt/host/c/`.
- **Legacy Docker Toolbox** — Boot2Docker VM with default credentials `docker`/`tcuser`, host always at the bridge gateway.
- **The host itself, without any socket access** — Moby < 20.10.9 leaves `/var/lib/docker/overlay2` traversable by unprivileged host users (CVE-2021-41091); SUID "docker helper" binaries (field-observed); sudo-scoped docker subcommands (three observed chains).

## Reading the framework's mind

Five behaviors that predict what a Docker target will do, each expanded in [[docker-bug-bounty|the methodology]]:

1. **Group membership is a root check.** `id` showing `docker` (gid 998 etc.) ends the privilege-escalation hunt — unless the daemon is rootless, in which case the socket lives under `/run/user/$UID/` and the win is only that user. The `lxd` group is the same check against the sibling container daemon: a privileged LXD container with the host root mounted is the standard chain.
2. **The socket is HTTP; you never need the CLI.** `curl --unix-socket /var/run/docker.sock http://localhost/version` answers access questions on stripped containers; a static docker binary plus `-H unix:///app/docker.sock` handles the rest (field-observed).
3. **Every create request accepts the host filesystem.** `Binds` and `--privileged` are honored verbatim by the root daemon — including from an SSRF inside a container on Docker Desktop (CVE-2025-9074).
4. **The bridge gateway is the host.** First address of the container's docker subnet (172.17.0.x default) — port-scan it, SSH to it, pivot through it (three observed engagements).
5. **Deleted is not gone.** Image layers are an audit trail; `dive` (or `docker save` + untar) recovers files removed in later layers (field-observed) — treat any distributed image as a credentials dump.

## Where to go next

[[docker-bug-bounty|Docker bug-bounty methodology]] — recon and daemon-access checks, socket/group/API exploitation, privileged and capability escapes, runc CVEs, registry and image abuse, pivoting, and the rootless-mode gate on every chain.

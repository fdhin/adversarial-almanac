---
tool: "nfs-security-tooling"
build-system: "python"
upstream: "https://github.com/hvs-consulting/nfs-security-tooling"
recipe: "upstream"
license: "GPL3"
---

# Installing nfs-security-tooling

> `nfs_analyze` + `fuse_nfs` — NFS misconfiguration detection and UID-spoofing FUSE mounts.

Source: hacktricks feed; upstream [`hvs-consulting/nfs-security-tooling`](https://github.com/hvs-consulting/nfs-security-tooling).

## Dependencies

Linux only. First install pkg-config, libfuse3-dev and python3-dev:

```bash
sudo apt update
sudo apt install pkg-config libfuse3-dev python3-dev
```

## Install with pipx

```bash
pipx install git+https://github.com/hvs-consulting/nfs-security-tooling.git
```

## Privileged ports

NFS servers commonly require client source ports below 1024. The tools need `cap_net_bind_service` or root to use them. Kali has no privileged ports by default; elsewhere either run as root or relax the floor temporarily:

```bash
sudo sysctl net.ipv4.ip_unprivileged_port_start=0
```

## fuse_nfs setup

Edit `/etc/fuse.conf` and uncomment `user_allow_other` before using `fuse_nfs` as a non-root user.

## Notes

- `nfs_analyze` leaves traces in server logs and rmtab — assume the scans are noticed.
- Technical background in the repo [wiki](https://github.com/hvs-consulting/nfs-security-tooling/wiki), including the privilege-escalation section.

Backlinks: [[networking]] · [[nfs-security-tooling]]

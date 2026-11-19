---
name: "mallory"
categories:
  - blackarch-proxy
version: "134.47094fb"
homepage: "https://github.com/justmao945/mallory"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "justmao945/mallory"
github-stars: 323
github-pushed: "2023-12-20"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mallory

> HTTP/HTTPS proxy over SSH (Go): serves local proxy ports and tunnels their traffic through a remote SSH server — a minimal egress pivot that needs nothing but `sshd` on the far side.

*No corpus evidence — derived from upstream documentation.*

## What it's for

When the only usable path out of (or into) a network is an SSH account: mallory runs on your workstation, authenticates to the remote sshd with a key, and serves two local HTTP proxy ports — a *smart* one that consults a domain block list and tunnels only listed domains (everything else connects directly), and a *normal* one that forwards all traffic through the SSH server. It is a transport pivot, not an interceptor: it does not inspect, modify or decrypt anything it carries (for traffic interception see mitm-relay or Burp upstream of it).

## Typical use cases

### Route selected domains through an SSH tunnel

```
mallory -config mallory.json
```

```json
{
  "id_rsa": "$HOME/.ssh/id_rsa",
  "local_smart": ":1315",
  "local_normal": ":1316",
  "remote": "ssh://user@198.51.100.10:22",
  "blocked": ["example.com", "target.example.com"]
}
```

Outcome: connects to the SSH server, then listens on the two configured ports: `localhost:1315` tunnels only the `blocked` domains and lets everything else go direct, `localhost:1316` sends all traffic through the tunnel — point a browser at the smart port, or `export http_proxy=http://localhost:1316` for terminal tools. Without `-config` the file is read from `$HOME/.config/mallory.json`.

### Reload the block list without a restart

```
mallory -reload
```

Outcome: signals the running instance (equivalent to `kill -USR2 <pid>`) to re-read the `blocked` list from the config file, so routing changes apply without dropping the SSH session; the list is also reloaded automatically when the file changes on disk.

### Check which suffix a domain matches

```
mallory -suffix www.example.com
```

Outcome: prints the registered domain suffix (here `example.com`) that the smart proxy matches against the block list — useful to confirm a host will actually be tunneled before adding an entry.

---
name: "orjail"
categories:
  - blackarch-defensive
version: "200.ae38ba2"
homepage: "https://github.com/orjail/orjail"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-09-26"
build-system: scripts
license: WTFPL
meta-checked: "2026-11-09"
github-repo: "orjail/orjail"
github-stars: 215
github-pushed: "2022-04-28"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# orjail

> Tor-enforcing jail that runs programs inside a dedicated network namespace.

## What it's for

orjail builds a separate network, PID, and mount namespace, launches a Tor instance bound to it, and installs host iptables rules so that anything executed inside can only egress through Tor — including DNS. It targets the weakness of LD_PRELOAD-based wrappers like torsocks (a statically linked binary simply bypasses the hook): here the Tor-only path is enforced at the namespace layer, so a leak attempt is blocked rather than merely intercepted. It needs root to set up but runs the jailed command as your user, and can additionally wrap the command in firejail. For whole-host routing use nipe instead, and for day-to-day anonymous browsing the Tor Browser remains the recommended option.

## Typical use cases

### Force a single command through Tor

Run one program with all its traffic Tor-only.

```
sudo orjail curl http://example.com
```

Outcome: curl executes inside the orjail namespace and the fetched page prints to your terminal — its traffic, DNS included, could only leave via the jail's Tor instance; running `sudo orjail ifconfig` shows the dedicated jail interface instead of the host's.

### Get an anonymous shell

Open an interactive session where every command is Tor-only.

```
sudo orjail -s
```

Outcome: Spawns your `$SHELL` inside the namespace; anything executed in that session is forced through Tor until the shell exits.

### Publish a hidden service

Expose a local service as an onion site without touching the host's Tor config.

```
sudo orjail -v -H 8080 -d hs "python3 -m http.server 8080"
```

Outcome: Tor is configured as a hidden service forwarding to local port 8080; the assigned .onion address is written to `hs/hostname` and the service stays reachable from the Tor network while the command runs.

### Add a security sandbox on top

Layer firejail's restrictions over the Tor-only network path.

```
sudo orjail -f thunderbird
```

Outcome: The command runs inside firejail joined to orjail's network namespace, combining firejail's filesystem/syscall sandboxing with enforced Tor egress; extra firejail options go in `--firejail-args "..."`.

### Keep the jail alive for reuse

Preserve the namespace and Tor instance after a command finishes.

```
sudo orjail -k ls
```

Outcome: The namespace and its Tor instance survive the command's exit, so a subsequent orjail invocation can join the same jail instead of paying setup cost again.

*No corpus evidence — derived from upstream documentation.*

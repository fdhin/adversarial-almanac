---
name: "crowbar"
categories:
  - blackarch-cracker
version: "v4.2.r1.g4b563dc"
homepage: "https://github.com/galkan/crowbar"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "galkan/crowbar"
github-stars: 1539
github-pushed: "2023-12-19"
github-archived: false
github-issues: 46
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crowbar

> Brute-forces the services normal password sprayers handle badly — RDP with NLA, SSH private-key auth, VNC keys, and OpenVPN — across subnets, user lists, and whole folders of keys.

## What it's for

Crowbar (formerly Levye) is a network brute-forcer for four authentication flows that are awkward to script: Remote Desktop with NLA, SSH public-key login, VNC, and OpenVPN username/password. Its SSH mode is the differentiator — instead of guessing passwords it sprays private keys (a single file or every key in a directory) against a user, which is how a key obtained on one host gets checked against the rest of a network. Targets are given as CIDR ranges, optionally pre-filtered with an nmap port scan so only reachable services are attacked. It does not do plain SSH password brute force; use a general-purpose brute-forcer for that.

## Typical use cases

### Spray an RDP subnet with user and password lists

```
crowbar -b rdp -s 198.51.100.0/24 -U users.txt -C passwords.txt -d
```

Outcome: the `-d` flag nmap-scans the range first and only attacks hosts with RDP reachable; every working pair is appended to `crowbar.out` as a line like `RDP-SUCCESS : 198.51.100.10:3389 - "user":password`, with the full attempt history in `crowbar.log`.

### Test a folder of SSH private keys against one user

```
crowbar -b sshkey -s 198.51.100.10/32 -u root -k keys/
```

Outcome: tries every key file in `keys/` (a single key path such as `~/.ssh/id_rsa` also works) as candidate credentials for root; any key that authenticates is reported in `crowbar.out`, revealing reused keys across servers.

### Authenticate to an OpenVPN endpoint

```
crowbar -b openvpn -s 198.51.100.10/32 -p 1194 -m client.ovpn -k ca.crt -u vpnuser -c 'Winter2024!'
```

Outcome: uses the client configuration (`-m`) and optional CA certificate (`-k`) to attempt real OpenVPN connections over TCP or UDP with each candidate credential; a working login is logged as a success line. Resolve the VPN host's address from the config's `remote` line first, since `-s` takes an IP or range.

### Log into RDP as a domain user

```
crowbar -b rdp -s 198.51.100.10/32 -u 'CORP\\jdoe' -c 'Password1!'
```

Outcome: authenticates as domain user `CORP\jdoe`; because backslash is an escape character, pass the name double-escaped (`CORP\\jdoe`) or in `user@domain` form, otherwise the domain part is silently mangled and every attempt fails.

### Attack VNC on a non-default port

```
crowbar -b vnckey -s 198.51.100.10/32 -p 5902 -k vnc-passwd
```

Outcome: runs the VNC attempt against the display on port 5902 using the password file given with `-k`, reporting any successful authentication in `crowbar.out` alongside the standard `crowbar.log` history.

*No corpus evidence — derived from upstream documentation.*

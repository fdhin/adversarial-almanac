---
name: "levye"
categories:
  - blackarch-cracker
version: "84.5406303"
homepage: "https://github.com/galkan/levye"
packaged-by: [blackarch]
blackarch-builddate: "2014-12-26"
build-system: scripts
license: custum
meta-checked: "2026-11-09"
github-repo: "arm13/levye"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 3
github-pushed: "2014-08-04"
github-archived: false
github-issues: 0
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# levye

> Brute-forcer for the authentication schemes mainstream tools skip: SSH private keys, VNC key files, RDP with NLA, and OpenVPN.

## What it's for

Levye ("crowbar" in Turkish) is a Python 2 brute-forcing tool by Gökhan Alkan for protocols that hydra-style username/password crackers handle poorly or not at all: SSH private-key authentication (try a pile of stolen or generated keys against a user), VNC key authentication, RDP with Network Level Authentication, and OpenVPN (config + certificate + credentials). Each mode is selected with `-b {sshkey,vnckey,rdp,openvpn}`; targets, users, passwords and keys can each be a single value or a file. It shells out to openvpn, xfreerdp, vncviewer and ssh rather than reimplementing the protocols. The same author later renamed and rewrote the tool as Crowbar (galkan/crowbar, Python 3) — prefer Crowbar on modern systems.

## Typical use cases

### Brute-force RDP on a single host

```
python2 levye.py -b rdp -s 198.51.100.10/32 -u administrator -c passwords.txt
```

Outcome: drives xfreerdp once per candidate password, logging each attempt; a working password is recorded as a hit in the output file (`-o`) and log (`-l levye.log`).

### Try SSH private keys instead of passwords

```
python2 levye.py -b sshkey -s 198.51.100.10/32 -u root -p 22 -k id_rsa
```

Outcome: attempts key-based SSH authentication; `-k` also accepts a directory, in which case every key file in it is tried — useful for replaying a set of recovered keys.

### Test VNC key authentication

```
python2 levye.py -b vnckey -s 198.51.100.10/32 -p 5901 -c vncpass.txt
```

Outcome: runs vncviewer per candidate VNC password and reports which one authenticates to the target display.

### Verify OpenVPN credentials against a server config

```
python2 levye.py -b openvpn -s 198.51.100.10/32 -m server.ovpn -c passwords.txt -u users.txt -k ca.crt -p 443
```

Outcome: runs openvpn with the supplied client config and CA certificate, iterating user/password pairs; successful tunnel authentication is flagged in the log.

*No corpus evidence — derived from upstream documentation.*

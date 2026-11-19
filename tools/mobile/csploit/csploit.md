---
name: "csploit"
categories:
  - mobile
homepage: "http://www.csploit.org"
github-repo: "cSploit/android"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: GPL3
github-stars: 3685
github-pushed: "2024-04-27"
github-commits-year: 0
gh-checked: "2026-11-14"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# csploit

> The classic all-in-one Android network pentesting app — mapping, service inspection, Metasploit-driven exploitation, and MITM, entirely from a rooted phone.

## What it's for

cSploit is the free-software descendant of dSploit (evilsocket's tool, fused with zANTI2 lineage): a rooted-Android app that turns a phone into a network assessment console. From one GUI you map the LAN, fingerprint hosts and services (nmap-based Service Inspector), launch exploits through an integrated Metasploit RPC daemon and open shells on successful hits, and run full man-in-the-middle attacks — password sniffing with protocol dissection, session hijacking, DNS spoofing, connection killing, plus real-time content rewriting (text/image replacement, JavaScript injection) against unencrypted pages. It is the reference point for "pentest from a phone" and is still the most-starred tool of its kind. Honest status: the developers have declared it end-of-life — no commits since April 2024, and it may not work on recent Android versions. It is admitted here as a dormant-famous rescue on owner call: historically essential, currently frozen, so verify it runs on your device's Android version before relying on it. Requires root, a FULL BusyBox install, and SuperSU.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Map the local network

```
Launch cSploit → wait for the host list → tap + to add off-subnet hosts
```

Outcome: on start the app scans and lists routers, computers, phones, and printers with a globe entry for the whole /24 subnet; **+** manually registers hosts outside the local network — every later action targets a selected list entry.

### Inspect a host's services

```
Select host → Service Inspector → Start
```

Outcome: an nmap-driven port and service scan of that host; the discovered services feed the exploit search and every later host action.

### Exploit a host via Metasploit and open a shell

```
Select host → vulnerability/exploit list → pick exploit → Launch → Sessions → Command shell
```

Outcome: the app's integrated Metasploit daemon runs the chosen module (the wiki walkthrough uses `exploit/unix/ftp/vsftpd_234_backdoor` against a deliberately vulnerable VM); a **Job started** message is followed by a **Command shell** entry under **Sessions** that opens an interactive console on the exploited host.

### Rewrite web text for a whole subnet

```
Select the /24 subnet entry → MITM → Custom Filter → fill From/To → OK
```

Outcome: ARP-based MITM plus a content filter — every plain-HTTP page viewed by any LAN user shows your replacement text where the original string was; tap the icon again to stop the attack.

### Replace images network-wide

```
Select the /24 subnet entry → MITM → Replace Images → pick a device image or Web URL
```

Outcome: substitutes your chosen image for images loaded over unencrypted connections across the MITM'd segment — the visual demonstration of why transport encryption matters.

---
name: "dsniff"
categories:
  - sniffing-spoofing
version: "2.4b1-34"
homepage: "https://www.monkey.org/~dugsong/dsniff/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-11-01"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "ggreer/dsniff"
github-stars: 351
github-pushed: "2022-11-27"
github-archived: false
github-issues: 11
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dsniff

> Dug Song's classic suite for sniffing cleartext insecurities: password sniffers, MITM helpers and traffic-shaping utilities.

## What it's for

dsniff is Dug Song's canonical collection of network audit tools demonstrating the insecurity of cleartext and weakly-encrypted protocols. One half passively watches shared or mirrored traffic and extracts what leaks: the dsniff password sniffer itself, URL/mail/file/message extractors. The other half creates the conditions to sniff on a switched LAN: arpspoof and macof divert traffic to the auditor's machine, dnsspoof forges name resolution, sshmitm and webmitm interpose on redirected connections, and tcpkill/tcpnice manage in-flight connections. TCP streams are reassembled with libnids, and most passive members accept `-p pcapfile` instead of `-i interface` to work offline on saved captures. Distro packages build from the maintained fork (ggreer/dsniff) of the original monkey.org sources, which compiles on modern systems. The tools are for auditing your own network; several (arpspoof, sshmitm) are themselves the attacks they demonstrate.

## Typical use cases

### dsniff — harvest cleartext credentials automatically

Let one sniffer watch dozens of protocols at once.

```
sudo dsniff -i eth0
```

Outcome: dsniff reassembles TCP streams and auto-detects FTP, Telnet, SMTP, HTTP, POP, IMAP, NNTP, SNMP, LDAP, Rlogin, NFS, SMB, Oracle SQL*Net, IRC and many other protocols, printing authentication data as it appears; `-w savefile` stores unique attempts in a Berkeley DB file instead of stdout.

### urlsnarf — log visited URLs in web-server format

Collect browsing history from the wire.

```
sudo urlsnarf -i eth0
```

Outcome: URLs seen in HTTP traffic are written in Common Log Format (as web servers use), ready for offline processing with standard log analyzers.

### mailsnarf — capture email to an mbox

Save messages passing through SMTP or POP.

```
sudo mailsnarf -i eth0
```

Outcome: complete email messages sniffed from SMTP and POP sessions are appended in Berkeley mbox format, openable in any mail reader (`mail -f`, etc.).

### filesnarf — extract files from NFS traffic

Recover files as they cross the network.

```
sudo filesnarf -i eth0
```

Outcome: files transferred over NFS are reassembled and written to the current working directory as regular files.

### msgsnarf — record chat sessions

Watch instant-messenger and IRC conversations.

```
sudo msgsnarf -i eth0
```

Outcome: messages from AIM, ICQ 2000, IRC and Yahoo! Messenger sessions are printed with sender and conversation context as they are sniffed.

### webspy — mirror a victim's browsing live

Follow a target's surfing in your own browser.

```
sudo webspy -i eth0 198.51.100.10
```

Outcome: URLs requested by client 198.51.100.10 are relayed to a local Netscape browser, which loads each page as the target visits it — a real-time demonstration of session visibility on cleartext HTTP segments.

### sshow — analyze encrypted SSH sessions

Extract timing and size metadata from SSH without decrypting it.

```
sudo sshow -i eth0
```

Outcome: SSH-1/SSH-2 traffic is analyzed and reported by authentication attempts, approximate password lengths and command-line lengths — password guessing and activity patterns remain visible through encryption.

### arpspoof — divert switched traffic to your machine

Defeat switch port isolation by answering ARP for another host.

```
sudo arpspoof -i eth0 -t 198.51.100.10 198.51.100.1
```

Outcome: ARP replies are forged so client 198.51.100.10 sends traffic meant for the gateway (198.51.100.1) to the auditing machine instead; with kernel IP forwarding enabled, the client's connectivity is unaffected while its traffic becomes sniffable. Ctrl-C restores normal ARP by stopping the replies.

### dnsspoof — forge DNS answers

Redirect name resolution for MITM setups.

```
sudo dnsspoof -i eth0 -f spoof.hosts
```

Outcome: DNS address and pointer queries matching entries in spoof.hosts (an /etc/hosts-style file of `IP name` lines) are answered with the spoofed addresses before the real resolver can reply — the front end for pointing clients at sshmitm or webmitm.

### macof — flood the switch's CAM table

Force some switches to fail open.

```
sudo macof -i eth0
```

Outcome: the segment is flooded with packets carrying random source MAC addresses until the switch's forwarding table overflows and it repeats traffic out all ports, turning the switched LAN into a hub for passive sniffing.

### sshmitm — intercept SSH logins

Proxy SSH connections redirected via dnsspoof.

```
sudo sshmitm 198.51.100.10
```

Outcome: an SSH interception proxy answers connections for the given host, capturing SSH v1 password logins and optionally hijacking interactive sessions; the client sees a new host key — the tell that this attack leaves behind.

### webmitm — intercept HTTPS sessions

Proxy HTTP and HTTPS traffic redirected via dnsspoof.

```
sudo webmitm
```

Outcome: redirected web connections are transparently proxied and decrypted with a forged certificate, exposing "secure" webmail logins and form submissions that would otherwise be encrypted end-to-end.

### tcpkill — cut in-progress connections

Terminate matched TCP sessions on the wire.

```
sudo tcpkill -i eth0 host 198.51.100.10
```

Outcome: matching TCP connections receive forged resets and die; the severity of the attack (default through `-9`) controls how persistently the resets are injected.

### tcpnice — throttle in-progress connections

Slow matched traffic instead of killing it.

```
sudo tcpnice -i eth0 host 198.51.100.10
```

Outcome: tiny TCP window advertisements (and optionally ICMP source-quench replies) are forged toward matching connections, throttling their throughput via active traffic shaping.

*No corpus evidence — derived from upstream documentation.*

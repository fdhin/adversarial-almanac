---
name: "sshttp"
gentoo-category: "net-proxy"
version: "0.25"
homepage: "http://c-skills.blogspot.com/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "stealth/sshttp"
github-stars: 894
github-pushed: "2023-06-22"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sshttp

> OSI-Layer5 switching daemon that hides SSH (and other services) behind an HTTP/HTTPS port.

## What it's for

sshttpd multiplexes two protocols on a single listening port: it peeks at the first bytes of each incoming connection and switches SSH traffic to the real sshd port and HTTP(S) traffic to the real webserver port — for when firewall policy leaves only one open port. Supported pairs include SSH/HTTP, SSH/HTTPS, SSH/SMTP, and HTTPS SNI-based multiplexing (including hiding extra named services per SNI name). No client or server software needs patching; it runs only on Linux with `IP_TRANSPARENT` and `nf_conntrack` support, drops to an unprivileged user in a chroot when built with capability support, and can run as a transparent gateway muxing a whole LAN's traffic (`-T` with the nf-tproxy ruleset). Since sshd is never directly exposed, it also works as a defence against SSH scanning and bruteforcing.

## Typical use cases

### Multiplex SSH and HTTP on port 80

Share the web port with sshd on a single host.

```
sshttpd -S 22 -L 80 -H 8080 -U nobody -R /var/empty
```

Outcome: sshttpd binds port 80, prints its port assignment and goes to background, using caps/chroot as the configured user; connections classified as SSH are relayed to port 22 and HTTP to 8080. Run the bundled `nf-setup` script first (edited for your device and ports) so the firewall routes ports 22/8080 through the muxer — note nf-setup firewalls port 22, so never run it from inside the SSH session it would cut off.

### Multiplex SSH and HTTPS on port 443

Hide sshd behind the TLS port.

```
sshttpd -S 22 -L 443 -H 4433 -U nobody -R /var/empty
```

Outcome: port 443 serves both the real HTTPS server (moved to 4433) and sshd (on 22); sshttpd distinguishes SSH from SSL sessions on the same listener. One instance cannot mix plaintext HTTP/SSH and HTTPS/SSH — run a second sshttpd on port 80 for the plaintext pair.

### Multiplex additional TLS services by SNI

Route extra hidden services on the HTTPS port per hostname.

```
sshttpd -S 0 -H 4433 -L 443 -N drops.v2:7350
```

Outcome: TLS connections carrying the SNI name `drops.v2` are switched to port 7350 while ordinary HTTPS traffic goes to 4433; `-S 0` disables the SSH leg, and multiple `-N name:port` switches mux several named services. All target ports must run on the same machine as the original destination.

### Mux SSH and SMTP on the mail port

Use the SMTP port when HTTP is not available.

```
sshttpd -S 22 -L 25 -H 2525
```

Outcome: connections to port 25 are switched between sshd (port 22) and the local SMTP daemon (listening on 2525); edit the bundled nf-setup to match these ports, and set `SMTP_DOMAIN`/`SSH_BANNER` in the Makefile so the generated SMTP banner matches your environment.

### Transparently mux a LAN's traffic from a gateway

Run the muxer on the gateway instead of on each host.

```
sshttpd -T -S 22 -L 80 -H 8080
```

Outcome: in transparent mode sshttpd proxies HTTP/SSH traffic for the internal LAN through the gateway, preserving original source addresses thanks to IP_TRANSPARENT; build the firewall from the bundled `nf-tproxy` template rather than nf-setup, checking device and IP values so you don't lock yourself out. Add `-6` (and the nf6-setup script) for IPv6.

*No corpus evidence — derived from upstream documentation.*

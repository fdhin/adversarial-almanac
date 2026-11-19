---
name: "sslsniff"
categories:
  - blackarch-sniffer
version: "0.8"
homepage: "https://github.com/moxie0/sslsniff"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-10-01"
build-system: autotools
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "moxie0/sslsniff"
github-stars: 574
github-pushed: "2017-12-17"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sslsniff

> Exploit-era TLS man-in-the-middle tool that terminates intercepted connections and serves forged certificates.

## What it's for

sslsniff (Moxie Marlinspike, 2009) is a man-in-the-middle proxy for SSL/TLS: once victims' traffic is redirected to it — typically ARP spoofing plus an iptables REDIRECT rule — it answers each HTTPS connection with a forged certificate, either generated on the fly and signed by a CA certificate you hold ("authority mode") or picked from a directory of pre-made certificates ("targeted mode"), while logging the decrypted traffic. It was built to demonstrate CA implementation failures such as null-prefix certificates and missing BasicConstraints validation, and it can additionally deny OCSP requests, log only HTTP POSTs, filter victims by browser fingerprint, and hijack Mozilla/addon update delivery. Do not confuse it with sslstrip by the same author ([[sslstrip]]): sslstrip is an HTTP proxy that downgrades HTTPS links to plain HTTP and never presents certificates, whereas sslsniff speaks TLS itself and forges certificates for it. Unmaintained since 2017 — for current interception testing use mitmproxy or SSLsplit.

## Typical use cases

### Run in authority mode

Forge a certificate for every intercepted domain, signed by a CA you control.

```
sslsniff -a -s 10000 -w sslsniff.log -c ca.pem
```

Outcome: listens on port 10000; each redirected TLS connection is answered with a freshly generated certificate signed by ca.pem, and the decrypted traffic is written to sslsniff.log.

### Run in targeted mode

Serve pre-generated certificates for specific hosts from a directory.

```
sslsniff -t -s 10000 -w sslsniff.log -m IPSCACLASEA1.crt -c certs/
```

Outcome: listens on port 10000 and presents the matching certificate from certs/ for the host the victim connects to (with IPSCACLASEA1.crt as the chain root), logging decrypted traffic to sslsniff.log.

### Redirect victims' HTTPS to the listener

Pair the listener with the firewall rule and ARP spoofing it expects.

```
iptables -t nat -A PREROUTING -p tcp --destination-port 443 -j REDIRECT --to-ports 10000
arpspoof -i eth0 -t 198.51.100.10 198.51.100.1
```

Outcome: with IP forwarding enabled, the victim's HTTPS traffic is redirected to the sslsniff listener and its plaintext appears in the log file.

### Attack only selected clients and deny OCSP

Restrict by browser fingerprint, block certificate-status checks, and trim logging.

```
sslsniff -a -s 10000 -w sslsniff.log -c ca.pem -f ff,ie -h 8080 -d -p
```

Outcome: clients are fingerprinted on an HTTP listener (port 8080) and only matching families (Firefox, IE) are attacked; OCSP requests are denied and only HTTP POST data is logged.

*No corpus evidence — derived from upstream documentation.*

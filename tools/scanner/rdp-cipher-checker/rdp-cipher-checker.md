---
name: "rdp-cipher-checker"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "https://labs.f-secure.com/tools/rdp-cipher-checker/"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-27"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rdp-cipher-checker

> F-Secure Python script that enumerates the encryption protocols an RDP server supports and the cipher strengths it allows under native RDP encryption.

## What it's for

Auditing the cryptography of a Remote Desktop / Terminal Services endpoint from
the network side, without credentials. It reports which encryption protocols
the server accepts and which cipher strengths are permitted when native
(Standard) RDP encryption is in use — the pre-TLS encryption layer that older
Windows RDP services still offer. Useful when hardening RDP exposure or
verifying that weak 40/56-bit native encryption has actually been disabled.

This is a 2013-era single-file Python 2 tool (BlackArch packages the script as
the `rdp-cipher-checker` command, depends on python2). The original download
(`labs.f-secure.com/assets/BlogFiles/rdp-check-ciphers.zip`) is no longer
served — the F-Secure Labs site was migrated and the asset is gone — and no
upstream source or usage documentation survives, so no verified command syntax
can be given here. For the same job on a live target, its companion tool
[[rdp-sec-check]] (the Portcullis Perl script, kept as a separate vault note)
documents both the protocol negotiation checks (Standard RDP vs TLS vs
CredSSP/NLA) and the native encryption strength enumeration
(40/56/128-bit, FIPS) with a maintained upstream and reproducible examples;
run it when you need actual scanner output rather than this historical script.

*No corpus evidence — derived from upstream documentation (archived F-Secure
Labs tool page and the BlackArch packaging); no runnable CLI is documented
anywhere upstream, so this note is description-only.*

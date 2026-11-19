---
name: "pcredz"
categories:
  - blackarch-scanner
version: "108.a07051d"
homepage: "https://github.com/lgandx/PCredz"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-07"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "lgandx/PCredz"
github-stars: 2595
github-pushed: "2026-03-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# pcredz

> Extracts credentials and authentication hashes from packet captures or a live interface.

## What it's for

PCredz (Laurent Gaffié) parses PCAP files or live captures, IPv4 and IPv6, and pulls out authentication material: NTLMv1/v2 challenge-response hashes (HTTP, SMB, LDAP, MSSQL, DCE-RPC), Kerberos AS-REQ pre-auth hashes, HTTP basic auth and form fields, FTP/IRC/SMTP/IMAP/POP3/LDAP/MSSQL logins, SNMP community strings, and optionally credit card numbers. Hashes are emitted in hashcat-ready formats and deduplicated by default. It does not attack anything itself — it turns captured traffic into a credential inventory for cracking or analysis.

## Typical use cases

### Harvest credentials from a packet capture

Extract every credential in a single pcap file.

```
pcredz -f capture.pcap
```

Outcome: parses the capture, prints found credentials (for example complete NTLMv2 hashes) to the console, and writes per-type files under `logs/` — `NTLMv1.txt` (hashcat `-m 5500`), `NTLMv2.txt` (`-m 5600`), `MSKerb.txt` (`-m 7500`), `FTP-Plaintext.txt`, `SNMPv1.txt` and the rest — plus `CredentialDump-Session.log` with the full timeline.

### Process a directory of captures recursively

Run over an entire tree of pcaps.

```
pcredz -d /forensics/network-captures/ -o /tmp/pcredz-output/
```

Outcome: walks the directory parsing every pcap file (one `Parsing ...` line per file) and writes the credential logs under the given output directory instead of the current one.

### Sniff credentials live off the wire

Capture and decode authentication traffic as it happens.

```
sudo pcredz -i eth0 -v
```

Outcome: live capture on eth0 (root required) with credentials printed in real time; `-v` repeats duplicates instead of suppressing them. Add `--exclude-host <ip>` to keep your own machine out of the results during interactive use.

### Cut the noise by disabling protocol parsers

Focus a parse on selected protocols.

```
pcredz -f capture.pcap --disable HTTP --disable SNMP
```

Outcome: the named parsers are skipped and their log files are not produced; repeatable for NTLM, FTP, IRC, LDAP, SMTP, Kerberos, SNMP and MSSQL — for example keep only cleartext credentials by disabling NTLM and Kerberos.

Corpus-grounded and verified against the upstream PCredz README (v2.1.0) and BlackArch packaging (`pcredz` = installed `Pcredz` script).

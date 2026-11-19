---
name: "ssltest"
gentoo-category: "net-analyzer"
version: "0.4"
homepage: "http://sites.google.com/site/lupingreycorner/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: AS IS
meta-checked: "2026-11-09"
github-repo: "stephenbradshaw/ssltest"
github-stars: 9
github-pushed: "2020-10-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ssltest

> Perl SSL server tester: enumerates supported protocols and ciphers on one host, colour-coded against ISM or PCI-DSS compliance.

## What it's for

ssltest.pl (lupin / Stephen Bradshaw, The Grey Corner; originally based on Cryptonark by Chris Mahns) tests an SSL-enabled host to determine which SSL protocols and ciphers it supports, then marks each enabled cipher against a compliance standard — DSD ISM or PCI-DSS — with terminal colour coding. It was written specifically to cope with badly behaved servers that hang on unsupported-protocol handshakes: connection timeouts are adjustable, and protocols can be tested individually (SSLv2, SSLv3, TLSv1). Output can be made greppable for reporting. It tests one host and port per run; the packaged version is 0.4, and the interface below is as documented on the author's page for the tool. The author keeps his own GitHub home for it (stephenbradshaw/ssltest, linked in the frontmatter), whose README carries a later 0.5 release marked deprecated in favour of testssl.

## Typical use cases

### Test a server's protocols and ciphers

```
ssltest www.example.com 443
```

Outcome: walks SSLv2, SSLv3 and TLSv1 cipher-by-cipher, printing every cipher the target accepts with its strength/compliance marking (colour-coded against the default ISM standard) — weak protocols showing any accepted ciphers are the finding.

### Check PCI-DSS compliance in greppable form

```
ssltest -p -g www.example.com 443 > ssl-report.txt
```

Outcome: same scan with PCI-DSS as the standard and semicolon-separated output written to `ssl-report.txt`, so compliance status survives being grepped or archived instead of living only in terminal colours.

### Restrict to one protocol and tune timeouts

```
ssltest -s -x 2 www.example.com 443
```

Outcome: tests only SSLv3 ciphers (`-r`/`-t` select SSLv2/TLSv1) with a 2-second connection timeout — the right shape for hosts that freeze on unsupported protocol attempts instead of closing them.

### List the ciphers the tool checks

```
ssltest --pci --list
```

Outcome: prints the tool's cipher table with each cipher's compliance marking and exits — no connection is made; useful to see exactly what is tested and how the standards rate it before scanning.

*No corpus evidence — derived from upstream documentation (author's archived tool page).*

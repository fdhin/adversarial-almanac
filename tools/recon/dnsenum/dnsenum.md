---
name: "dnsenum"
categories:
  - blackarch-recon
version: "1.2.4.2"
homepage: "https://github.com/fwaeytens/dnsenum/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "fwaeytens/dnsenum"
github-stars: 711
github-pushed: "2019-10-08"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsenum

> Classic Perl DNS enumerator: records, zone transfers, scraping, bruteforce and reverse lookups in one pass.

## What it's for

dnsenum (by Filip Waeytens) is the long-standing multithreaded Perl script
for enumerating everything a domain's DNS will give up. In one run it can
pull the host's addresses and NS/MX records, attempt zone transfers against
every nameserver, scrape search engines for additional subdomains, bruteforce
subdomains from a dictionary with optional recursion on NS-bearing hits,
expand and whois the surrounding netblocks, and reverse-resolve the resulting
IP ranges — writing discovered blocks to `<domain>_ips.txt`. It does not
exploit anything; it is pure passive-plus-query enumeration, and it remains
the standard first DNS pass on engagements precisely because every technique
is in one flag set. Google scraping is the part most likely to fail (captchas),
and XML output needs the optional XML::Writer Perl module installed.

## Typical use cases

### Enumerate a domain's DNS in one pass

The default sweep: records plus everything discoverable.

```
dnsenum example.com
```

Outcome: prints the host address, nameservers and MX records, attempts AXFR
against each nameserver, then reverse-looks-up results; discovered IP blocks
are written to example.com_ips.txt.

### Bruteforce subdomains via a chosen resolver

Point every query at one server and dictionary-walk the zone (the pattern
seen most often in the wild).

```
dnsenum --dnsserver 198.51.100.10 -f /usr/share/seclists/Discovery/DNS/namelist.txt example.com
```

Outcome: all enumeration queries go to the resolver at 198.51.100.10 (for
example the domain's own authoritative server found earlier), and every entry
of the wordlist is tested as a subdomain of example.com, with hits listed as
they resolve. Swap in any subdomain dictionary; the seclists namelist is the
usual companion.

### Quick profile with the --enum shortcut

Sane speed-oriented defaults in one flag.

```
dnsenum --enum example.com
```

Outcome: runs the enumeration with the preset combination equivalent to
5 threads, 15 search-engine result pages (kept low to dodge captchas) and
whois expansion of the C-class ranges — the recommended balance between
coverage and not tripping rate limits.

### Save subdomains and a machine-readable report

Emit files for the next tool in the chain.

```
dnsenum --enum --subfile subs.txt -o dnsenum.xml example.com
```

Outcome: besides the console output, every discovered subdomain is written
to subs.txt (ready to feed a resolver or HTTP prober) and the full result
set to dnsenum.xml for tooling — the XML file requires the optional
XML::Writer module to be installed.

Corpus-grounded: the resolver-plus-wordlist pattern above reflects real
recorded usage; all flags verified against the upstream `dnsenum.pl` source.

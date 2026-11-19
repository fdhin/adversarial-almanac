---
name: "seclists"
categories:
  - blackarch-wordlist
version: "2026.1.r441.gc5a0b8c"
homepage: "https://github.com/danielmiessler/SecLists/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "danielmiessler/SecLists"
github-stars: 74589
github-pushed: "2026-11-07"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1225
gh-activity-checked: "2026-11-09"
---

# seclists

> The security tester's companion dataset: curated lists of usernames, passwords, directories, subdomains, fuzzing payloads and web shells that other tools consume by file path.

## What it's for

SecLists is not a program — it is a repository of reference wordlists that fuzzers, brute-forcers and scanners read as input (`-w`/`-L`/`-P` style arguments). Distro packages (Kali, BlackArch, AUR, nixpkgs) install it at `/usr/share/seclists`; a manual clone is often placed at `/usr/share/wordlists/seclists`. Every use case below is therefore a consumer command pointed at a SecLists file.

Directory taxonomy (top level):

- `Discovery/DNS/` — subdomain and hostname lists: `subdomains-top1million-5000.txt`, `subdomains-top1million-110000.txt`, `bitquark-subdomains-top100000.txt`, `namelist.txt`, `dns-Jhaddix.txt`.
- `Discovery/Web-Content/` — directory, file, extension and parameter lists: `common.txt`, `big.txt`, `combined_directories.txt`, the `raft-*` families, `DirBuster-2007_directory-list-2.3-*.txt`, `burp-parameter-names.txt`, `quickhits.txt`, and technology lists under `Programming-Language-Specific/` (e.g. `Java-Spring-Boot.txt`).
- `Discovery/SNMP/` — community-string lists (`common-snmp-community-strings.txt`).
- `Fuzzing/` — payload corpora: `LFI/LFI-Jhaddix.txt`, `LFI/Linux/LFI-gracefulsecurity-linux.txt`, `Databases/SQLi/Generic-SQLi.txt`, `special-chars.txt`, `alphanum-case-extra.txt`, plus `XSS/` and `User-Agents/`.
- `Passwords/` — `Common-Credentials/` (`darkweb2017_top-1000.txt`, `xato-net-10-million-passwords-*.txt`, `top-passwords-shortlist.txt`), `Default-Credentials/`, `Leaked-Databases/` (rockyou), `Permutations/`, `WiFi-WPA/`.
- `Usernames/` — `xato-net-10-million-usernames.txt` and `Names/` (per-country top given/family names plus `names.txt`).
- `Web-Shells/`, `Payloads/`, `Pattern-Matching/` (secret-detection regexes), `Miscellaneous/`, and `.bin/` (wordlist generators and mutators).

Older releases used flatter names (`Passwords/darkweb2017-top1000.txt`, `Discovery/Web-Content/spring-boot.txt`, `Fuzzing/generic-sqli.txt`); current trees reorganize these into the subdirectories shown above, so tab-complete the path on your install before scripting around it.

## Typical use cases

### Enumerate virtual hosts and subdomains

Fuzz the Host header of a wildcard-responding web server with a DNS subdomain list.

```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -u http://target.example.com -H 'Host: FUZZ.target.example.com' -fs 42
```

Outcome: ffuf substitutes each candidate into the Host header and filters out the baseline wildcard response by size (here 42 bytes), printing hostnames whose response differs — live vhosts to resolve and probe. `gobuster vhost -u http://target.example.com -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt` performs the same sweep with gobuster's vhost mode.

### Brute-force hidden directories and files

Point a content-discovery scanner at the Web-Content directory lists, sized from quick (`common.txt`) to exhaustive (`raft-large-*`, `DirBuster-2007_directory-list-2.3-big.txt`).

```
gobuster dir -u http://target.example.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt -q -b 403,404
```

Outcome: gobuster requests each path, silences 403/404 noise, and prints discovered directories with their status codes and sizes. The same list slots into `ffuf -w <list> -u http://target.example.com/FUZZ` or `feroxbuster --url http://target.example.com -w /usr/share/seclists/Discovery/Web-Content/common.txt`.

### Hunt technology-specific endpoints

Use a list tailored to the stack instead of a generic one once the framework is fingerprinted.

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/Programming-Language-Specific/Java-Spring-Boot.txt \
     -u http://target.example.com/FUZZ -ic -t 100
```

Outcome: probes only paths known from Java Spring Boot deployments (actuator and friends) and auto-calibrates wildcard responses, quickly separating a real framework endpoint from boilerplate 404s. `quickhits.txt` serves the same purpose for high-signal "interesting file" hits.

### Discover hidden request parameters

Enumerate parameter names a server understands by fuzzing a GET parameter position with the Burp parameter-name list.

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
     -u 'http://target.example.com/admin/?FUZZ=test' -fs 178
```

Outcome: candidate parameter names that change the response (different size than the baseline 178 bytes) are printed as found parameters — follow up by fuzzing each discovered parameter's value.

### Brute-force a login form with password lists

Drive a POST login with a common-passwords list, using response filtering to spot the successful attempt.

```
wfuzz -c -w /usr/share/seclists/Passwords/Common-Credentials/darkweb2017_top-1000.txt \
      -d 'username=admin&password=FUZZ&submit=Login' \
      -u http://target.example.com/login --hw 84
```

Outcome: wfuzz submits one candidate password per request; `--hw 84` hides responses with the failed-login word count (measure it on a known-bad attempt first), so the surviving entries are the credentials that logged in. `hydra -l admin -P /usr/share/seclists/Passwords/Common-Credentials/top-passwords-shortlist.txt 198.51.100.10 http-post-form '/login:user=^USER^&pass=^PASS^:invalid'` does the same against a form login.

### Spray usernames from username corpora

Attack user-controlled inputs (logins, API endpoints) with real-world username lists, or enumerate local accounts from per-country name lists.

```
hydra -L /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -p 'Password1' 198.51.100.10 ssh
```

Outcome: hydra tries one common password against every username in the corpus over SSH and prints any valid pairs. For host-level user enumeration, `finger-user-enum -U /usr/share/seclists/Usernames/Names/names.txt -t 198.51.100.10` tests first-name accounts against the finger service.

### Fuzz local-file-inclusion paths

Substitute traversal payload files into a vulnerable parameter.

```
ffuf -w /usr/share/seclists/Fuzzing/LFI/Linux/LFI-gracefulsecurity-linux.txt \
     -u 'http://target.example.com/download.php?file=FUZZ' -fs 26
```

Outcome: each candidate path (including traversal sequences and sensitive Linux files) is requested; responses deviating from the baseline size reveal readable files. `Fuzzing/LFI/LFI-Jhaddix.txt` is the larger alternative for deeper sweeps.

### Enumerate SNMP community strings

Community-string brute force against an SNMP service.

```
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt 198.51.100.10
```

Outcome: onesixtyone probes the target with each community string and prints the strings the agent answers to, along with the system description — a valid community string unlocks `snmpwalk` enumeration.

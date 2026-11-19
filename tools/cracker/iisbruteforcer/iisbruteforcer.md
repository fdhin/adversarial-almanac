---
name: "iisbruteforcer"
categories:
  - blackarch-cracker
version: "15"
homepage: "http://www.open-labs.org/"
packaged-by: [blackarch]
blackarch-builddate: "2014-12-23"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# iisbruteforcer

> Multithreaded online dictionary attack against IIS HTTP authentication

## What it's for

iisbruteforcer (binary `iisbf`) tests weak passwords on HTTP-protected areas of Microsoft IIS servers — Basic and NTLM authentication, over HTTP or HTTPS — using up to 360 parallel threads. IIS's Basic-authentication implementation tolerates an unusually high probing rate, which is what the tool is tuned for; other servers such as Apache work too but at lower efficiency. Each run attacks one username against one URL with a dictionary file, and a bundled dictionary tree (from 1,000 to 1,000,000 words) ships alongside. It targets HTTP authentication realms, not HTML login forms. The original site is long gone, so the packaged version and its bundled dictionaries are the surviving reference.

## Typical use cases

### Default dictionary run

```
iisbruteforcer http://target.example.com/protected/
```

Outcome: attacks the protected URL as user `administrator` (the default) with dict/dict.txt, printing `FOUND!!` with `==> USER: administrator - PASS: <password>` and the candidate's position in the dictionary when a valid credential is hit; HTTPS targets are attacked simply by giving an https:// URL.

### Tuned run: user, dictionary, threads

```
iisbruteforcer https://target.example.com/protected/ -u user -f dict/bigdict.txt -t 64
```

Outcome: attacks as user `user` with the 1.4-million-word bigdict.txt across 64 threads; on packaged installs the dictionaries live under /usr/share/iisbruteforcer/dict/. `-X <method>` selects GET (default), HEAD, POST, PUT or PROPFIND as the request method and `-d <data>` attaches POST data.

### Attack an NTLM-authenticated directory

```
iisbruteforcer http://target.example.com/protected/ -u user -f dict/10000.txt -N
```

Outcome: switches authentication probing to NTLM (slower per attempt but the only scheme many IIS servers expose) and reports cracked credentials the same way as Basic runs.

*No corpus evidence — derived from upstream documentation.*

---
name: "lumma-stealer-dllhost-hollowing-c2-domains-payload-extraction-analysis"
categories:
  - backdoor
version: "git (2026-09-01)"
homepage: "https://github.com/kaandemir993/lumma-stealer-dllhost-hollowing-c2-domains-payload-extraction-analysis"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "kaandemir993/lumma-stealer-dllhost-hollowing-c2-domains-payload-extraction-analysis"
github-stars: 12
github-pushed: "2026-09-01"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# lumma-stealer-dllhost-hollowing-c2-domains-payload-extraction-analysis

> A published reverse-engineering analysis of one Lumma Stealer sample — dropper strings, C2 domains, native-API hollowing behavior and payload-extraction walkthrough, kept as reference material rather than a runnable program.

## What it's for

Reading, not running. This repository is a structured analysis report of a Lumma Stealer sample (dropper `setup.exe`, process hollowing into `dllhost.exe`), produced with Process Hacker, Binary Ninja and x64dbg. It documents: memory strings from the dropper indicating camera/microphone privacy tampering, driver preinstall and memory mapping; a candidate C2 domain list observed in dropper memory (`zhtytomyr.ua`, `adobeaemcloud.com`/`.net`, `Vagrancy.virus.cc`, plus abused hosting/AWS domains); the native APIs behind hollowing and injection (`NtCreateThreadEx`, `NtWriteVirtualMemory`, `NtCreateSection`, `NtLoadDriver`); ALPC-based communication; and clipboard-hijacking/keylogging capability (`SetClipboardData`, `NtUserGetAsyncKeyState`, `NtUserSetWindowsHookEx`). It closes with detection recommendations and links the analyzed sample on MalwareBazaar. There is no CLI, library or service — the value is as an IOC and technique reference: feed the domains to blocklists and alert on the named native-API calls occurring inside `dllhost.exe`. For hands-on work, fetch the linked sample and repeat the workflow with your own Process Hacker/Binary Ninja setup.

*No corpus evidence — derived from upstream documentation.*

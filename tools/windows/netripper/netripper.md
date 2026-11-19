---
name: "netripper"
categories:
  - blackarch-windows
version: "84.c763bd0"
homepage: "https://github.com/NytroRST/NetRipper"
packaged-by: [blackarch]
blackarch-builddate: "2022-06-26"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "NytroRST/NetRipper"
github-stars: 1388
github-pushed: "2022-06-17"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netripper

> Post-exploitation traffic interceptor that hooks Windows APIs inside a target process to capture plaintext and pre-encryption traffic.

## What it's for

NetRipper (released at Defcon 23 by Ionut Popescu) addresses post-exploitation on a Windows box where you hold only low-privileged rights: a normal sniffer sees encrypted frames, but NetRipper injects a DLL that hooks network and encryption functions inside a chosen application (tested against PuTTY, WinSCP, browsers, Outlook, SQL Server Management Studio), capturing data before it is encrypted and after it is decrypted. Plugins trim the capture: plaintext-only, a byte limit per request, or a string finder that flags data containing tokens like credentials. It is not a passive sniffer and not a credentials store dumper — it records the process's network I/O from inside.

## Typical use cases

### Generate a configured DLL

Build the DLL that will be injected, with capture options baked into it.

```
NetRipper.exe -w DLL.dll -l TEMP -p true -d 4096 -s user,pass
```

Outcome: writes DLL.dll configured to save captures under the user's TEMP directory, keep only plaintext data, limit each request/response to 4096 bytes, and flag captures containing the strings `user` or `pass`.

### Inject into a target process

Hook a running application and start collecting its traffic.

```
NetRipper.exe DLL.dll firefox.exe
```

Outcome: the DLL is injected into firefox.exe; as the application communicates, captured requests and responses are written to files under the configured location.

### Run it as a Metasploit post module

Automate DLL selection and injection from an existing Meterpreter session.

```
use post/windows/gather/netripper/netripper
set SESSION 1
set PROCESSNAMES firefox.exe,chrome.exe
run
```

Outcome: the post module picks the right DLL bitness and injects into each named process, producing the same on-host capture files as the standalone executable.

*No corpus evidence — derived from upstream documentation (NytroRST/NetRipper README).*

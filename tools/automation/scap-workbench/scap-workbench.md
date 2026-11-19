---
name: "scap-workbench"
categories:
  - blackarch-automation
version: "1.2.1"
homepage: "https://www.open-scap.org/tools/scap-workbench/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-05-04"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "OpenSCAP/scap-workbench"
github-stars: 237
github-pushed: "2024-02-13"
github-archived: true
github-issues: 75
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scap-workbench

> Qt GUI over the OpenSCAP scanner: open a SCAP data stream, tailor a profile to your environment, scan a local or remote machine, and save the results — without hand-writing oscap commands.

## What it's for

Making SCAP compliance scanning approachable. SCAP Workbench wraps the openscap library in a desktop application: it loads SCAP source data streams (with built-in integration that automatically offers installed SCAP Security Guide content), lets you toggle individual rules on and off to tailor a profile to your environment, runs the scan against the local machine or a remote one over SSH, and produces results and HTML reports. It also saves your tailoring as a customization file you can reuse or ship. It is purely defensive — an audit and hardening-prep tool; it attacks nothing. Development stopped (the upstream repo is archived since 2024) and the modern OpenSCAP team points users at the `oscap` CLI and `oscap-ssh` for new deployments, but the GUI still works for single-machine scans and profile tailoring. The full user manual is embedded (`Help -> User Manual`, or `/usr/share/doc/scap-workbench/user_manual.html`).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open the tool against a specific data stream

```
scap-workbench /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml
```

Outcome: the main window opens with that data stream loaded — its profiles listed for selection, the rule tree browsable, and the Scan/Customize/Remote-Sync controls live. Running `scap-workbench` with no argument starts the same UI from an open-file dialog instead; any XCCDF file or SCAP source data stream (SDS) path can be passed the same way.

### Tailor a profile and scan from the GUI

Launch the tool, pick a profile, and use the Customize button to deselect rules that do not apply to the machine.

```
scap-workbench
```

Outcome: after selecting a profile and choosing Customize, the rule tree opens for editing (enable/disable per rule, adjust values); saving writes a tailoring file, Scan runs openscap against the tailored profile, and on completion the tool offers to view the HTML report and save ARF/results files. That saved tailoring file is the artifact to keep — it encodes your site's deviations from the baseline and can be reused on the next machine.

---
name: "edb-debugger"
categories:
  - forensics
  - reverse-engineering
version: "1.3.0-2.2+b1"
homepage: "http://www.codef00.com/projects#debugger"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali, aur]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "eteran/edb-debugger"
github-stars: 2980
github-pushed: "2026-10-27"
github-archived: false
github-issues: 66
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 75
gh-activity-checked: "2026-11-09"
---

# edb-debugger

> Pointer note: this is Evan's Debugger (edb), the Qt-based graphical x86/x86-64 debugger — the same tool filed as `edb` in the debugger category.

Same upstream project as [[edb]] (github.com/eteran/edb-debugger; the author's project page is codef00.com). See the [[edb]] note for the full description and typical use cases: starting a binary under the debugger (`edb --run ./target`), attaching to a running process (`edb --attach PID`), and redirecting the debuggee's stdin/stdout.

Not to be confused with `edb-id:` / `edb verified:` header fields found in exploit-db documents — those refer to Exploit-DB entry metadata, not this debugger.

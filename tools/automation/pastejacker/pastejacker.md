---
name: "pastejacker"
categories:
  - blackarch-automation
version: "12.ed9f153"
homepage: "https://github.com/D4Vinci/PasteJacker"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "D4Vinci/PasteJacker"
github-stars: 403
github-pushed: "2024-10-14"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pastejacker

> Automates clipboard-poisoning (pastejacking): serves a decoy web page that silently swaps whatever the visitor copies for your payload one-liner, which executes itself the moment they paste into a terminal.

## What it's for

Weaponizing the copy/paste path into a shell. The tool serves an HTML page with bait content (package-install instructions, for example); when the target copies from it, JavaScript hooks the copy event (or CSS-hidden spans, which need no JavaScript) and swaps the clipboard for your payload line, crafted so that pasting into a terminal both executes it and clears the terminal — the whole swap-and-run lands in under a second. Three payload styles: an msfvenom backdoor downloaded and executed silently (wget/certutil per OS), a background reverse-netcat one-liner, or your own custom liner (e.g. Metasploit web-delivery) with output-hiding touches applied. It is a social-engineering payload delivery tool: it needs a target who will copy from a page you control and paste into a terminal, and modern browsers/terminals have shipped partial mitigations. Python 3, needs root, and msfvenom only if you use the backdoor option.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Launch the pastejacking console

```
sudo pastejacker
```

Outcome: opens the interactive console (root required). You pick the attack method (JavaScript copy-event hook, or the two no-JavaScript CSS variants), the payload type, and the serving options; the tool then stands up the decoy page. The JavaScript method replaces anything copied on the page and auto-executes on paste; the CSS-span variants need the target to select all the page text but work with JavaScript disabled (transparent-text variant fails on Chrome/Opera).

### Pick the payload the copied line will carry

Inside the console, choose between the built-in payload generators:

```
sudo pastejacker
```

Outcome: selecting the msfvenom option builds a backdoor locally and makes the poisoned liner download and execute it on the target (nothing printed to their terminal); the reverse-netcat option instead embeds a one-liner that calls back to your listener in the background; the custom-liner option wraps any command you supply (such as a web-delivery payload) with output suppression. For the first two the tool prompts for the callback host/port, and you run your matching handler/listener on your side.

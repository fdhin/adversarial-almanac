---
name: "brida"
categories:
  - webapp
homepage: "https://github.com/federicodotta/Brida"
github-repo: "federicodotta/Brida"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 1902
github-pushed: "2025-10-30"
github-commits-year: 0
gh-checked: "2026-11-14"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# brida

> Burp Suite extension that bridges Burp and Frida — call the mobile app's own encryption/signing methods on the traffic you're tampering with, from inside Burp.

## What it's for

Brida solves the mobile-assessment wall where requests are encrypted, signed, or obfuscated by code inside the app: instead of reverse-engineering the routine and reimplementing it as a Burp plugin, you let the application do the work. The extension runs Frida exports against the live process (via [[frida]] on the device and the [[python-frida]]/pyro4 plumbing on your workstation) and wires them into Burp — process requests/responses through the app's own decrypt/encrypt/sign functions, add custom tabs that decode selected traffic, invoke exports from context menus, and build complete custom plugins graphically. It also ships an analysis view (a tree of Java/ObjC classes, methods, imports, exports where you attach inspection and tamper hooks), a built-in JavaScript editor, a library of ready-made hooks (SSL-pinning bypasses, root/jailbreak-detection bypasses, iOS data-protection inspection), and a "Generate Stubs" tool that emits Java/Python glue for external Burp extensions. Supports every Frida platform (Android, iOS, Windows, macOS, Linux, QNX); Brida ≥ 0.6 requires Frida ≥ 17. Activity is low (no commits in the last year) but the project is the standard tool of its niche.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Install the extension

```
Burp → Extender → BApp Store → Brida, Burp to Frida bridge → Install
```

Outcome: the Brida tab appears in Burp. Equivalent manual path: download the release JAR, then `Extender → Extensions → Add →` select `BridaXX.jar`. BApp Store versions lag GitHub releases.

### Inspect and hook binary methods graphically

```
Brida tab → Analysis → expand the class tree → right-click a method → add inspection or tamper hook
```

Outcome: a tree of the target app's Java/ObjC classes and methods (plus imports/exports); an inspection hook prints arguments and return value every time the method runs, a tamper hook changes the return value live — no Frida script written by hand.

### Decrypt/encrypt traffic with the app's own functions

```
Brida tab → link Frida exports → wire them into a custom plugin processing requests/responses
```

Outcome: Burp hands intercepted HTTP messages to your chosen Frida export — the app's real decrypt routine — so Proxy/Repeater/Intruder see plaintext, and your edits are re-encrypted and re-signed by the same code before being sent.

### Reuse common mobile hooks without scripting

```
Brida tab → Hooks and functions → pick e.g. an SSL-pinning or root-detection bypass → enable
```

Outcome: bundled, community-maintained Frida hooks for Android and iOS security controls run through the GUI — the fastest path to making a pinned or root-checking app testable.

### Generate glue for external extensions

```
Brida tab → Generate Stubs → choose Java or Python
```

Outcome: emits stub code that external Python/Java Burp extensions paste in to call Brida-exported app functions — for cases where the graphical plugin engine is not expressive enough.

---
tool: "autorize"
build-system: "python"
upstream: "https://github.com/portswigger/autorize"
recipe: "upstream"
license: "custom:unknown"
---

# Installing autorize

> Autorize — automatic authorization enforcement detection extension for Burp Suite.

Source: pentest-book feed; BApp Store mirror [`portswigger/autorize`](https://github.com/portswigger/autorize); the author's development repository is [`Quitten/Autorize`](https://github.com/Quitten/Autorize) (Barak Tawily).

## From the BApp Store (recommended)

```
Burp: Extensions -> BApp Store -> install "Autorize"
```

Outcome: the extension installs with its Python environment handled for you; a new **Autorize** tab appears in Burp.

## Manual installation (Python extension + Jython)

Autorize is a Jython (Python 2 for the JVM) extension, so Burp needs the Jython standalone JAR before it can load the `.py` file:

```
1. Download Burp Suite:        https://portswigger.net/burp/download.html
2. Download Jython Standalone: https://www.jython.org/download.html
3. Burp -> Extender -> Options -> Python Environment -> Select File -> jython standalone JAR
4. git clone https://github.com/Quitten/Autorize.git
5. Burp -> Extender -> Extensions -> Add -> Extension type: Python -> select Autorize.py
```

Outcome: the Autorize tab appears, confirming the extension loaded; if it does not, re-check step 3 — a missing Jython environment is the usual failure.

## Notes

- No distro packages this extension (checked 2026-11-13); it only runs inside Burp Suite.
- Upstream declares no license in the repository (checked 2026-11-13).

Backlinks: [[webapp]] · [[autorize]]

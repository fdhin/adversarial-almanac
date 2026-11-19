---
tool: "param-miner"
build-system: "java"
upstream: "https://github.com/PortSwigger/param-miner"
recipe: "upstream"
license: "see repository"
---

# Installing param-miner

> Burp Suite extension that brute-forces hidden parameters, headers and cookie names — the standard tool for finding web cache poisoning inputs.

Source: hacktricks feed; upstream [`PortSwigger/param-miner`](https://github.com/PortSwigger/param-miner).

## Install into Burp Suite (BApps)

Requires Burp Suite 2021.9 or later:

```
Burp -> Extender/BApps -> search "Param Miner" -> Install
```

## Build the extension jar yourself

```bash
git clone https://github.com/PortSwigger/param-miner.git
cd param-miner
./gradlew build fatjar        # Windows: gradlew.bat build fatjar
```

Then load `build/libs/param-miner-all.jar` via
Burp -> Extender -> Extensions -> Add (Extension type: Java).

## Not yet packaged

No distro packages this Burp extension (checked 2026-11-13).

Backlinks: [[webapp]] · [[param-miner]]

---
tool: "apk-mitm"
build-system: "node"
upstream: "https://github.com/niklashigi/apk-mitm"
recipe: "upstream"
license: "MIT"
---

# Installing apk-mitm

> Auto-patches Android APKs for HTTPS inspection — network security config plus pinning removal, no root.

Source: hacktricks feed; upstream [`niklashigi/apk-mitm`](https://github.com/niklashigi/apk-mitm).

## Requirements

- Node.js 14+
- Java 8+ (Apktool and uber-apk-signer run on it)

## Install from npm

```shell
npm install -g apk-mitm
```

Also packaged in the AUR (`apk-mitm`).

## Bundles on Linux

Patching `.xapk`/`.apks` bundles additionally needs `zip` and `unzip` installed.

Backlinks: [[mobile]] · [[apk-mitm]]

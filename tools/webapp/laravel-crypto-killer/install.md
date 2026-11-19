---
tool: "laravel-crypto-killer"
build-system: "python"
upstream: "https://github.com/synacktiv/laravel-crypto-killer"
recipe: "upstream"
license: "MIT"
---

# Installing laravel-crypto-killer

> Exploits Laravel's `decrypt()`/`encrypt()` once you hold the `APP_KEY`: forge serialized payloads for RCE, decrypt cookies, or brute-force reused keys.

Source: hacktricks feed; upstream [`synacktiv/laravel-crypto-killer`](https://github.com/synacktiv/laravel-crypto-killer).

## From source

```bash
git clone https://github.com/synacktiv/laravel-crypto-killer.git
cd laravel-crypto-killer
./laravel_crypto_killer.py -h
```

Plain Python 3 script — no pip install step; bundled `wordlists/` folder
feeds the `bruteforce` action.

## Companion tool

The RCE path needs [phpggc](https://github.com/ambionics/phpggc)
(`./phpggc Laravel/RCE9 system id -b`) with a PHP version matching the
target where possible.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[webapp]] · [[laravel-crypto-killer]]

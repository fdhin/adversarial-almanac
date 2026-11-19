---
tool: "turbo-intruder"
build-system: "java"
upstream: "https://github.com/portswigger/turbo-intruder"
recipe: "upstream"
license: "AGPL3"
---

# Installing turbo-intruder

> Turbo Intruder — Burp Suite extension for sending large numbers of HTTP requests with exceptional speed and scriptable complexity.

Source: pentest-book feed; upstream [`portswigger/turbo-intruder`](https://github.com/portswigger/turbo-intruder). Usage docs: [Turbo Intruder: Embracing the Billion-Request Attack](https://portswigger.net/blog/turbo-intruder-embracing-the-billion-request-attack) plus the repo's `docs/` API reference.

## From the BApp Store (easiest)

In Burp Suite open `Extensions → BApp Store`, select **Turbo Intruder**, and install. Burp Pro and Community both work.

Outcome: the extension loads and a Turbo Intruder window becomes available; right-click any request → `Extensions → Turbo Intruder` to send it to the attack window.

## Manual build and JAR load

```bash
git clone https://github.com/portswigger/turbo-intruder.git
cd turbo-intruder
./gradlew build fatjar        # Windows: gradlew.bat build fatjar
```

Outcome: produces `build/libs/turbo-intruder-all.jar`; load it in Burp via `Extensions → Installed → Add` and select the JAR file as the extension type "Java".

## Notes

- Turbo Intruder is licensed AGPL-3.0 (releases up to and including 1.62 were Apache-2.0).
- Other PortSwigger extensions (e.g. HTTP Request Smuggler) bundle Turbo Intruder as a dependency — building those requires `turbo-intruder-all.jar` at the root of their source tree.
- No distro packages this extension (checked 2026-11-13); it only runs inside Burp Suite.

Backlinks: [[webapp]] · [[turbo-intruder]]

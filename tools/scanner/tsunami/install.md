---
name: "tsunami-install"
categories:
  - scanner
---

# Installing tsunami

Java/Gradle project — the supported distribution is Docker:

```bash
docker pull ghcr.io/google/tsunami-scanner-full
docker run -it --rm ghcr.io/google/tsunami-scanner-full bash
```

Everything lands under `/usr/tsunami` (engine, plugins, callback server). Building outside the Docker environment is explicitly unsupported by upstream; per-category and scratch plugin images (`ghcr.io/google/tsunami-scanner-core`, `tsunami-plugins-*`) exist for composing custom images — see the upstream build-and-run docs at https://google.github.io/tsunami-security-scanner/howto/howto.

If you use the callback server, publish its port when starting the container (`docker run -p`).

Verification: inside the container, `tsunami --help` prints the engine's options.

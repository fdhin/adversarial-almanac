---
name: "mpt-install"
categories:
  - mobile
---

# Installing mpt

Python package `mptsec` (preferred via pipx so the CLI and deps stay isolated):

```bash
pipx install mptsec
# or straight from the repo:
pipx install git+https://github.com/ByteSnipers/mobile-pentest-toolkit --include-deps
```

Plain pip works too (`pip install mptsec`, ideally inside a venv). Update with `mpt --update` or `pipx install ... --force`; uninstall with `pipx uninstall mptsec`.

First launch creates `~/.mpt/settings.json` — set `install-dir` (tool directory), `package-name`, `app` (APK path), and the `proxy.host`/`proxy.port` pair there. `mpt --install-tools` then populates the toolchain; a connected Android device with ADB/USB debugging enabled is needed for device-side flags.

---
name: "leaker-install"
categories:
  - recon
---

# Installing leaker

Go binary (requires go1.24+):

```bash
go install -v github.com/vflame6/leaker@latest
```

Prebuilt release binaries are on the project's GitHub Releases page.

After install, `leaker` runs immediately, but most sources need API keys: create a provider config file (`-p/--provider-config`) per the upstream wiki's Configuration page (https://github.com/vflame6/leaker/wiki/Configuration). Results are cached in a local SQLite DB by default (`--db`, `--no-write-db`, `-C` to clear).

Verification: `leaker --version` and `leaker -L` (lists sources).

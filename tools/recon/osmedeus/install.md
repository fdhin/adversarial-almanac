---
tool: "osmedeus"
build-system: "go"
upstream: "https://github.com/j3ssie/Osmedeus"
recipe: "upstream"
license: "MIT"
---

# Installing osmedeus

> Osmedeus — a modern orchestration engine for security: declarative YAML workflows, sandboxed runners, distributed execution.

Source: pentest-book feed; upstream [`j3ssie/Osmedeus`](https://github.com/j3ssie/Osmedeus). Docs at [docs.osmedeus.org](https://docs.osmedeus.org) — see the [Installation](https://docs.osmedeus.org/installation/) page for advanced setups.

## Official install script (recommended)

Pulls the engine and its workflow/data files onto the host.

```bash
curl -sSL http://www.osmedeus.org/install.sh | bash
```

Outcome: installs the `osmedeus` binary and workflow assets; verify with `osmedeus --help` and `osmedeus workflow list`.

## Prebuilt / releases

Release binaries are published on [upstream releases](https://github.com/j3ssie/Osmedeus/releases).

## Docker

Run the engine out of a container, keeping scan output on the host.

```bash
docker run --rm osmedeus:latest --help
docker run --rm -v $(pwd)/output:/root/workspaces-osmedeus \
    osmedeus:latest run -f general -t example.com
```

Outcome: the first command prints CLI help from the image; the second runs the `general` flow against `example.com` with all workspaces written to `./output` on the host via the bind mount (per the upstream README — build/pull the `osmedeus:latest` image as described in the current docs).

## Not yet packaged

No mainstream distro packages osmedeus at admission time (checked 2026-11-13); use the install script, a release binary, or Docker above — the [upstream docs](https://docs.osmedeus.org/installation/) also cover building from source.

Backlinks: [[recon]] · [[osmedeus]]

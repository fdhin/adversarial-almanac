---
name: "slimtoolkit"
categories:
  - misc
version: "1.40.4-0parrot1"
homepage: "https://github.com/slimtoolkit/slim"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "slimtoolkit/slim"
github-stars: 23503
github-pushed: "2026-09-19"
github-archived: false
github-issues: 213
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# slimtoolkit

> Profiles a container image under its real workload and rebuilds it containing only what it actually used (formerly DockerSlim; command `slim`).

## What it's for

SlimToolkit shrinks and hardens container images. Its core `build` command runs the target image while a sensor records every file, executable and syscall it touches (optionally driving it with HTTP probes, custom commands or a compose file), then writes a new image from just that runtime footprint, along with AppArmor/seccomp profiles generated from the observed behavior. Companion commands inspect and manage images: `xray` statically dissects an image's layers and contents and reverse-engineers its Dockerfile, `lint` checks Dockerfiles, `profile` gathers the runtime report without producing a minified image, `merge` combines minified images, and `registry` pushes/copies images. It reduces size and attack surface; it is not a vulnerability scanner. Running `slim` with no arguments opens an interactive prompt.

## Typical use cases

### Minify an existing image

Take a working fat image and produce a minimal version of it.

```
slim build my/sample-app
```

Outcome: the image is started under observation, then a minified `my/sample-app.slim` is built and tagged; size before/after and a full `slim.report.json` are written to the working directory.

### Minify straight from a Dockerfile

Go from source to a hardened image in one step, without a pre-built fat image.

```
slim build --dockerfile Dockerfile --tag my/app:min .
```

Outcome: builds the image from the Dockerfile first (intermediate tag gets a `.fat` suffix), profiles it, and tags the minified result `my/app:min`.

### Keep app paths reachable through profiling

Exercise HTTP endpoints during the observation window so their code and dependencies are recorded as used.

```
slim build --http-probe-cmd /api/info --http-probe-cmd POST:/submit my/sample-app
```

Outcome: the listed endpoints are called inside the container during profiling; their files survive minification instead of being stripped, and results appear in the build report.

### Dissect an image you did not build

See what is inside a fat image and how it was constructed.

```
slim xray my/sample-app
```

Outcome: static analysis of the image — layers, files, metadata and a reconstructed Dockerfile — printed and saved to a report file.

### Lint a Dockerfile

Check container instructions against SlimToolkit's rule set before building.

```
slim lint Dockerfile
```

Outcome: prints each matching check with a snippet; `--list-checks` (without a target) enumerates the available checks.

*No corpus evidence — derived from upstream documentation.*

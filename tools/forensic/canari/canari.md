---
name: "canari"
categories:
  - blackarch-forensic
version: "3.3.10"
homepage: "https://pypi.org/project/canari/#files"
packaged-by: [blackarch]
blackarch-builddate: "2022-08-26"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "malleum-inc/canari3"
github-stars: 145
github-pushed: "2021-04-21"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# canari

> Python framework for developing, debugging, and packaging Maltego transforms.

## What it's for

Canari is a transform framework for Maltego: it lets you write Maltego
transforms — the per-entity enrichment steps Maltego runs when you pivot on
a node — in Python instead of Java. A transform is a small class that
receives an entity and returns response entities, and Canari supplies the
scaffolding around it: package skeletons, Maltego profile (`.mtz`)
generation so transforms appear in the Maltego UI, a console for debugging
transforms outside Maltego, local execution via its dispatcher, and Canari
Plume for serving transforms remotely (including dockerized Plume
deployments). It is a development and distribution layer, not an
intelligence source — your transforms are only as good as the data sources
you point them at. Development of the current (3.x) series has been quiet
since its mid-2010s releases under the redcanari/Malleum maintainers.

## Typical use cases

### Scaffold a new transform package

Generate the package layout with an example transform to start from.

```
canari create-package mypackage
```

Outcome: interactively prompts for author/email/description, then writes a
`mypackage/` package skeleton (including an example transform), ready for
`canari create-transform` to add more.

### Add a transform to a package

Create a new transform module inside the package.

```
canari create-transform resolve_domain
```

Outcome: writes a new `resolve_domain` transform class into the working
package and updates its dependencies — you then implement
`do_transform()` to return response entities.

### Debug a transform without Maltego

Run a transform from the terminal against a test value and inspect the
returned entities.

```
canari debug-transform resolve_domain example.com
```

Outcome: executes the transform locally with `example.com` as the entity
value and prints the XML/entities it returns, exposing errors that would
otherwise surface silently inside Maltego.

### Generate the Maltego import profile

Produce the `.mtz` that registers the package's transforms in a Maltego
client.

```
canari create-profile mypackage
```

Outcome: writes `mypackage.mtz`; importing it in Maltego (Import
Configuration) installs the transform entities and settings so the
transforms can be run from the Maltego UI or its transform distribution
server.

### List available transforms

See what a package (or the current environment) provides.

```
canari list-transforms
```

Outcome: prints every transform found in installed/local packages with its
package prefix, for wiring into Maltego profiles.

*No corpus evidence — derived from upstream documentation.*

---
name: "threatspec"
categories:
  - blackarch-defensive
version: "0.5.0"
homepage: "https://github.com/threatspec/threatspec"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-14"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "threatspec/threatspec"
github-stars: 388
github-pushed: "2020-12-30"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# threatspec

> Continuously generates a threat model from `@`-annotation comments kept inside the source code itself.

## What it's for

threatspec treats threat modeling as code: developers annotate source files with comments such as `@mitigates WebApp:FileSystem against unauthorised access with strict file permissions`, plus `@accepts`, `@exposes`, `@transfers` and `@review`, mapping code constructs to components, threats and controls in a `threatspec.yaml`-configured project. The tool parses the annotated repository into a JSON threat model and renders a markdown report with a Graphviz diagram, so the threat model stays in sync with the code as both evolve instead of rotting in a separate document. Annotations work in common single- and multi-line comment styles, inline, and as an `x-threatspec` extension key inside YAML/JSON (OpenAPI-compatible). It documents and structures threat models — it does not scan code for vulnerabilities or misconfigurations.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Initialise threat modeling in a repository

Turn a source tree into a threatspec project.

```
threatspec init
```

Outcome: prints `Initialising threatspec...` and creates a `threatspec.yaml` configuration file in the current directory describing the project and the source paths to parse; edit it to name the project and point at the right directories.

### Build the threat model from annotated source

Parse the code and collect the annotations into a model.

```
threatspec run
```

Outcome: annotated source files are parsed and a `threatmodel/` directory is written containing `threatmodel.json` plus the library files `threats.json`, `controls.json` and `components.json` — the mitigations, acceptances, exposures and connections declared in code comments, ready for reporting or diffing between runs.

### Generate the threat model report

Render the model as a document people can actually read.

```
threatspec report
```

Outcome: writes `ThreatModel.md` (a structured threat model document built from the collected annotations) and `ThreatModel.md.png` (a Graphviz visualisation of components and their relationships) in the working directory; report generation requires Graphviz to be installed.

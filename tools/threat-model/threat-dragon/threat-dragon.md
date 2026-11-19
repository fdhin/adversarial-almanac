---
name: "threat-dragon"
categories:
  - blackarch-threat-model
version: "2.2.0"
homepage: "https://owasp.org/www-project-threat-dragon/"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-16"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "OWASP/threat-dragon"
github-stars: 1679
github-pushed: "2026-11-04"
github-archived: false
github-issues: 102
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1092
gh-activity-checked: "2026-11-09"
---

# threat-dragon

> OWASP Threat Dragon — an open-source threat modeling application for drawing data-flow diagrams and recording threats and mitigations.

## What it's for

Threat Dragon is OWASP's Production-status threat modeling tool (Apache-2.0), created by Mike Goodwin and rewritten in Vue.js for version 2.x. You draw a system as a data-flow diagram (processes, data stores, data flows, trust boundaries), the tool suggests threats for diagram elements (STRIDE-style per element), you record mitigations and counter-measures against each threat, and reports can be rendered from the model. It is primarily a web application (model storage on the local filesystem or in GitHub/GitLab/Bitbucket repositories); the desktop editions are Electron builds that keep models entirely local, and the BlackArch package repackages the official desktop `.deb` as the `threat-dragon` launcher. It is a GUI application with no scripting surface — but models are plain JSON files, so they diff and version-control cleanly.

## Typical use cases

### Launch the modeling desktop

```
threat-dragon
```

Outcome: opens the Threat Dragon desktop window. From there: create a new model or open an existing `.json` model, drag elements onto the diagram to describe the system, open each element's threat editor to enumerate suggested threats and record mitigations, then save — the model lives on your local filesystem as a JSON file you can commit alongside the code it describes.

*No corpus evidence — derived from upstream documentation.*

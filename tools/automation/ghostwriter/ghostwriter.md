---
name: "ghostwriter"
categories:
  - automation
homepage: "https://ghostwriter.wiki/"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: python
github-repo: "GhostManager/Ghostwriter"
github-stars: 1963
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
license: BSD-3-Clause
---

# ghostwriter

> Self-hosted engagement platform for offensive-security teams: findings library, infrastructure tracking, and one-click Word report generation.

## What it's for

Ghostwriter (sponsored and maintained by SpecterOps) is a Django web application a consulting or red-team operation hosts for itself. It centralizes the whole engagement lifecycle: clients and projects, a reusable findings library, covert infrastructure (servers and domain names used during assessments, with health monitoring), evidence files, and deadlines — and its reporting engine turns that structured data into polished deliverables. Reports are generated as DOCX via Jinja2 templates you customize to your own brand, with XLSX, PPTX and JSON export as well, so writers stop copy-pasting between documents and findings stay consistent across every report the team ships. It also logs C2 activity automatically through its Mythic and Cobalt Strike integrations, tracks which domains and servers are burned or in use, and exposes everything through a GraphQL API for wiring into your own project-management and CI workflows. Enterprise features include role-based access control, single sign-on and multi-factor authentication. It is a management and reporting platform, not a scanning tool — it does not test anything itself; deployment is Docker Compose (see the install notes), and day-to-day use happens entirely in the web UI, so there is no user-facing CLI to document.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Ship a report from the findings library

1. Create a project (or clone a previous engagement's).
2. Attach findings from the shared library; edit per-engagement details and drop evidence files.
3. Generate the deliverable from your Jinja2 DOCX template.

```
Ghostwriter UI: Projects → <client>/<project> → Findings → Assign from library
               → Reports → Generate (DOCX; XLSX/PPTX/JSON also available)
```

Outcome: a branded DOCX assembled from structured finding data — every report the team produces draws from the same library, so wording and severity stay consistent across engagements.

### Track covert infrastructure before it burns you

1. Register the assessment's servers and domains as infrastructure attached to the project.
2. Let Ghostwriter's health monitoring watch them (activity, categorization, expiration).
3. Check the infrastructure dashboard before reusing an asset on the next engagement.

```
Ghostwriter UI: Infrastructure → Domains/Servers → attach to active project
```

Outcome: a live inventory of which domains and VPS boxes are in use, idle, or burned — so a reused domain does not link two engagements together.

### Wire engagement data into your own tooling

```
POST https://ghostwriter.example.com/graphql/   (cookie or API-token auth)
```

Outcome: projects, findings and infrastructure are queryable/mutable through the GraphQL API the platform exposes for custom project-management and reporting pipelines — pull engagement state into a tracker or push auto-collected evidence in, without touching the UI.

---
name: "pwndoc"
categories:
  - automation
homepage: "https://pwndoc.github.io/pwndoc/"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: node
github-repo: "pwndoc/pwndoc"
github-stars: 2928
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
license: MIT
---

# pwndoc

> Self-hosted pentest reporting application: collaborative findings, a shared vulnerability library, and customizable DOCX report generation.

## What it's for

PwnDoc is a web application for penetration-testing teams that turns the reporting grind into structured data: findings are written once into audits, vulnerabilities live in a shared multi-language library that everyone reuses (with CVSS v3/v4 scoring, custom fields and custom sections), and the final deliverable is generated from a customizable DOCX template instead of hand-assembled in Word. Multiple users can edit an audit simultaneously with real-time collaboration and threaded comments, and reports pass through a review/approval workflow before release; retest and multi-audit workflows cover follow-up engagements. Beyond authoring it bundles quality-of-life features testers otherwise cobble together: spellcheck and grammar check via an optional LanguageTool container (with custom rules), AI-assisted writing with configurable prompts (OpenAI, Anthropic, DeepSeek, Ollama and AWS Bedrock providers), built-in and AI-assisted quality checks on audits and vulnerability templates, encrypted backup and selective restore, two-factor TOTP authentication, and custom roles with granular permissions. It is reporting infrastructure, not a testing tool — it never talks to targets; deployment is Docker-based (see the install notes) and all day-to-day use happens in the web UI, so there is no user-facing CLI.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Write an audit collaboratively

1. Create the audit from a client + audit template; set the assessment type and dates.
2. Invite reviewers; multiple editors work the same audit with real-time sync and comments.
3. Push the audit through the review workflow; retest findings feed a follow-up audit.

```
PwnDoc UI: Audits → New → select client/template → collaborate (multi-user, live)
```

Outcome: one structured audit document the whole team edits simultaneously — findings carry CVSS v3/v4 scores and custom fields instead of living in scattered Word files.

### Reuse the shared vulnerability library

1. Author the vulnerability once (multi-language title/description, remediation, severity guidance, custom fields/sections).
2. Reference it from any audit; per-audit edits do not mutate the library entry.

```
PwnDoc UI: Vulnerabilities → New (or edit shared entry) → Data → Languages
```

Outcome: the team accumulates a curated vulnerability knowledge base — new audits pull polished write-ups instead of re-drafting the same finding for the tenth client.

### Generate the deliverable from a custom DOCX template

1. Customize a DOCX template with the templating syntax (docxtemplater-style placeholders).
2. Map audit sections/findings to template fields.
3. Generate the report; missing-data checks flag template gaps before delivery.

```
PwnDoc UI: Settings → Templates (upload .docx) → Audits → Report generation
```

Outcome: a client-ready DOCX rendered from the structured audit — template changes propagate to every future report without touching past findings.

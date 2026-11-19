---
name: "attackgen"
categories:
  - mobile
version: "git (2026-11-02)"
homepage: "https://github.com/mrwadams/attackgen"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "mrwadams/attackgen"
github-stars: 1258
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 136
gh-activity-checked: "2026-11-09"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 18
---

# attackgen

> Generate tailored incident-response scenarios and tabletop exercises from MITRE ATT&CK / ATLAS threat intelligence using an LLM.

## What it's for

AttackGen is a Streamlit web application (plus an MCP server) for incident-response testing. It takes a threat actor group, an ATLAS AI attack case study, or a hand-picked selection of ATT&CK/ATLAS techniques, combines that with your organisation's industry and size, and has a large language model generate a complete incident response scenario — kill chain, injects, success criteria, metrics, artefacts and rules of engagement — downloadable as Markdown along with a Detection & Response report (matching ATT&CK detections, log sources, mitigations) and an ATT&CK Navigator layer. Generation works against OpenAI, Anthropic, Google, Mistral, Groq, or any OpenAI-compatible endpoint (Ollama, LM Studio, Azure OpenAI, OpenRouter), routed through LiteLLM. It is not a penetration-test report generator: the output is defensive exercise material, not attack documentation.

## Typical use cases

### Run the web interface locally

```
streamlit run 00_👋_Welcome.py
```

Outcome: starts the AttackGen app on local port 8501; set a provider API key in the Setup sidebar, choose a threat group on the Threat Group Scenarios page, then generate and download the scenario.

### Run the containerized app

```
docker run -p 8501:8501 mrwadams/attackgen
```

Outcome: the same web app served from the upstream container image at `http://localhost:8501`, with no local Python environment needed.

### Expose AttackGen to an MCP client

```
attackgen-mcp
```

Outcome: starts the AttackGen MCP server on stdio; MCP clients gain tools to list threat groups and ATLAS case studies, resolve kill chains, build detection reports and Navigator layers, and — with a per-call API key — generate full Markdown scenarios without the web UI.

### Register the MCP server with Claude Code

```
claude mcp add attackgen -- python -m mcp_server
```

Outcome: an `attackgen` MCP server entry is added to Claude Code, making the scenario-generation tools available to it in later sessions.

*No corpus evidence — derived from upstream documentation.*

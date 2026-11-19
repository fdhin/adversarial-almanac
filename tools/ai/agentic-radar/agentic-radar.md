---
name: "agentic-radar"
categories:
  - ai
homepage: "https://github.com/splx-ai/agentic-radar"
github-repo: "splx-ai/agentic-radar"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: Apache-2.0
github-stars: 1069
github-commits-year: 4
gh-checked: "2026-11-14"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# agentic-radar

> Security scanner for agentic AI workflows — parses LangGraph/CrewAI/n8n/OpenAI-Agents/Autogen code into a graph of agents, tools and MCP servers, maps them to known vulnerabilities, and emits an HTML report.

## What it's for

Agentic Radar (SPLX) analyzes the source of an agentic LLM application and produces a shareable HTML security report: a workflow visualization, the list of external and custom tools each agent can reach, the MCP servers in use, and a table mapping detected tools to known vulnerabilities — mapped against the OWASP Top 10 for LLM Applications and the OWASP Agentic AI threats-and-mitigations catalog. Reach for it when auditing or onboarding someone else's multi-agent codebase: it answers "what can this system actually do, and which capabilities are dangerously exposed" before you read a line. The static scan and vulnerability mapping run completely locally — source is not uploaded anywhere; the optional extras do call LLMs: `--harden-prompts` rewrites detected system prompts into structured best-practice prompts for the report, and the `test` subcommand executes the workflow at runtime against adversarial inputs (prompt injection, PII leakage, harmful-content and fake-news generation) with an oracle LLM judging each result, driven by the built-in suite or your own YAML test definitions. A GitHub Actions workflow example wires scans into CI. Apache-2.0, pip-installable.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan an agentic codebase

```
agentic-radar scan crewai -i ./crews -o report.html
```

Outcome: Parses the CrewAI project under ./crews, builds the agent/tool/MCP-server graph, maps tools to known vulnerabilities, and writes report.html — the framework argument accepts langgraph, crewai, n8n, openai-agents or autogen.

### Harden the system prompts it finds

```
agentic-radar scan openai-agents --harden-prompts -i ./examples -o report.html
```

Outcome: Additionally rewrites every detected system prompt into a structured, best-practice prompt and includes it in the report; needs OPENAI_API_KEY in the environment or passed via `--env-file` (a .env file is never auto-loaded).

### Run adversarial runtime tests against a workflow

```
agentic-radar test openai-agents "./agent_workflow.py"
```

Outcome: Injects into the workflow entrypoint and runs the built-in adversarial suite, then prints a per-agent table — test type, injected input, agent output, pass/fail, and a short explanation; requires OPENAI_API_KEY or AZURE_OPENAI_API_KEY.

### Test with a custom attack suite

```
agentic-radar test openai-agents --config custom_tests.yaml "./agent_workflow.py"
```

Outcome: Runs your YAML-defined tests (each with name, input, and a success_condition judged by the oracle LLM); set `include_default_tests: false` in the file to drop the built-ins.

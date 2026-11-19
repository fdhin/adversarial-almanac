---
name: "agentic-radar-install"
categories:
  - ai
---

# Installing agentic-radar

```sh
pip install agentic-radar
agentic-radar --version   # verify the install
```

Framework extras — needed for the runtime `test` feature and richer predefined-tool descriptions:

```sh
pip install "agentic-radar[crewai]"           # crewai-tools needs Python >=3.10,<3.13
pip install "agentic-radar[openai-agents]"
```

Prompt hardening and runtime testing need `OPENAI_API_KEY` (or `AZURE_OPENAI_API_KEY`) in the environment, or an explicit `--env-file path/to/.env`; the static scan runs fully offline. A ready-to-paste GitHub Actions workflow lives in the repo under `examples/github_workflow/`.

Backlinks: [[ai]] · [[agentic-radar]]

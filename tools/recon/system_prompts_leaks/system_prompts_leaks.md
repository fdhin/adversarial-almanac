---
name: "system_prompts_leaks"
categories:
  - recon
version: "git (2026-11-06)"
homepage: "https://github.com/asgeirtj/system_prompts_leaks"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "asgeirtj/system_prompts_leaks"
github-stars: 71081
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 713
gh-activity-checked: "2026-11-09"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 22
---

# system_prompts_leaks

> Reference archive of full leaked system prompts from ChatGPT, Claude, Gemini, Grok and 30+ other AI products, versioned and updated daily.

## What it's for

This is a dataset, not a program — a GitHub repository (asgeirtj/system_prompts_leaks) collecting the hidden system prompts of major AI assistants and coding agents in full text, with every captured version kept since May 2025. Coverage spans Anthropic (Claude web/desktop/mobile, Claude Code and its subagents/skills, artifacts and integrations), OpenAI (ChatGPT variants, Codex, voice products), Google (Gemini, Antigravity IDE/CLI), xAI Grok, Perplexity, DeepSeek, Cursor, GLM, MiniMax, Kimi, Qwen and more, plus a Misc/ folder for smaller products. The content is organized as per-vendor directories holding one markdown file per product/model version, including auxiliary prompts such as safety classifiers, tool prompts, skills and agent definitions.

Read it directly in the repository (or grep a local copy) when researching what instructions a given assistant actually operates under: prompt-injection defense design, red-teaming behavioral limits, comparing how vendors constrain tools and output formats, or tracking how a product's instructions evolve between model releases. It is curated documentation to consult — there is no CLI to run, and the repository is the interface.

*No corpus evidence — derived from upstream documentation (repository README).*

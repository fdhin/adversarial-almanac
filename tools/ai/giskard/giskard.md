---
name: "giskard"
categories:
  - blackarch-ai
version: "3.0.1.r23.g12c06e8"
homepage: "https://github.com/Giskard-AI/giskard-oss"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "Giskard-AI/giskard-oss"
github-stars: 5947
github-pushed: "2026-11-02"
github-archived: false
github-issues: 78
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 401
gh-activity-checked: "2026-11-09"
---

# giskard

> Open-source evaluation and vulnerability scanning for AI agents and LLM applications — write tests as scenarios, or scan an agent automatically.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Making LLM/agent quality testable in code. Two styles: declarative scenarios — you describe interactions and attach checks (groundedness, and the like) that run like unit tests — and an automated vulnerability scan that probes a target agent for issues without you writing cases. The target is just a Python callable (your model, RAG pipeline or agent function), so it slots into CI like any test suite. Use it before shipping a chatbot or agent to catch hallucination-prone answers, injected-instruction compliance and regressions between model or prompt versions.

## Typical use cases

### Write a groundedness check as a scenario

```
from giskard.checks import Scenario, Groundedness

def get_answer(inputs: str) -> str:
    return "Paris"  # replace with your model / agent

scenario = (
    Scenario("test_france_capital")
    .interact(inputs="What is the capital of France?", outputs=get_answer)
    .check(Groundedness(name="answer is grounded"))
)
```

Outcome: a named, runnable test that passes the question through your callable and evaluates the answer with the Groundedness check — green/red like a unit test, and composable into a suite that runs against every prompt or model change.

### Scan an agent for vulnerabilities automatically

```
import asyncio
from giskard.scan import vulnerability_scan

async def my_agent(inputs: str) -> str:
    return f"Echo: {inputs}"  # your agent / model call

asyncio.run(vulnerability_scan(
    target=my_agent,
    description="A customer support chatbot for an e-commerce platform"))
```

Outcome: giskard generates and fires probes at your agent (no hand-written cases), then produces a report of detected vulnerabilities — the quick first pass before you invest in hand-built scenarios.

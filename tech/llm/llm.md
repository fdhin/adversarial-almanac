---
technology: "LLM / prompt-injection attack surface"
kind: overview
audience: offensive-security
status: upstream-only
family: ai-ml
layer: inference-time application layer — chat interfaces, agents, RAG pipelines, and tool/function-calling integrations around large language models (the model-format/supply-chain side is the sibling unit [[ml-models]])
source: "six2dez/pentest-book (others/llm-ai-ml-prompt-testing.md, no license — rewritten with attribution), accessed 2026-11-13"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — risk taxonomy verified against OWASP GenAI LLM Top 10 2025 (LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM03 Supply Chain, LLM04 Data and Model Poisoning, LLM05 Improper Output Handling, LLM06 Excessive Agency, LLM07 System Prompt Leakage, LLM10 Unbounded Consumption), PortSwigger Web Security Academy (web LLM attacks, SSRF analogy, direct/indirect input methodology), and Joseph Thacker's AI-application hacking guide (sources/sinks methodology, system-prompt recon) + rewritten from six2dez/pentest-book (others/llm-ai-ml-prompt-testing.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# LLM / prompt-injection attack surface

> The model cannot tell instructions from data — everything lands in one context window, so every place content enters is a place commands enter.

## What it is

This unit covers language-model *applications* as an attack surface: chat endpoints, customer-support bots, RAG pipelines over private documents, and agents wired to APIs. The framing fixed by OWASP's 2025 taxonomy is that the model is a component inside an application whose inputs, tools, and outputs are all yours to probe — PortSwigger's blunt summary holds: attacking an LLM integration is structurally like SSRF, abusing a server-side system to reach data and APIs you cannot touch directly. The sibling unit [[ml-models]] owns the other half of AI attack surface — weaponized model *files* and format-level supply chain — while this page owns everything that happens at the prompt and tool layer at inference time.

The core defect is conflation. A system prompt, a user's message, a retrieved document, a tool's return value, and text inside an uploaded image are all rendered into the same token stream with no privilege separation. Guardrails are trained preferences and string filters bolted around that stream, not a security boundary — which is why the entire injection family (direct override, role-play jailbreak, encoded payloads, multi-modal carriers) keeps working across every provider despite years of hardening. What replaces "patched or not" is a responsibility model: the app owner decides what the model may see, call, and emit, and most of the exploitable space lives in those decisions.

The operator's second observation: the model is now the authorization path. Tools, plugins, and function calls run with credentials the *application* holds, so a successful injection spends the app's authority — OWASP calls the over-granted version Excessive Agency. And whatever the model emits often flows unsanitized into a browser, a SQL layer, or a shell: Improper Output Handling turns a model's text into XSS, SQLi, or command injection downstream. The injection is the entry; the sinks are the impact.

## The anatomy that matters on an engagement

    inputs (all conflated in one context window):
      system prompt (secrets, rules, API docs) · user message · RAG-retrieved docs ·
      tool/function results · uploaded files · images (OCR'd text, EXIF) · audio transcripts
    → the model (no instruction/data separation; guardrails = preferences + filters)
    → actions: tool/function calls running with the APP's credentials (delete account,
      send email, query internal API, browse URL, read file)
    → outputs: rendered markdown/HTML · SQL/shell input · downstream API arguments
                (unsanitized = classic injection sinks)
    → exfil: tool calls to attacker URLs · markdown image renders · reply text itself

Three structural properties drive most findings:

- **Every content source is an instruction channel.** Direct prompt, retrieved page, uploaded PDF, image steganography-text, audio background voice — the model obeys whichever string is most persuasive, regardless of origin.
- **System prompts are recon, not protection.** They carry API catalogs, tool descriptions, and sometimes credentials; leaking one (LLM07) usually maps the whole attack surface in a single response.
- **Impact lives outside the model.** The model merely emits tokens — the application's tool permissions and output handling decide whether that becomes data theft, account takeover, or stored XSS.

## Where you'll meet it

- **Customer-facing chatbots** wired to account and order APIs — the classic excessive-agency target: "delete my account" and "upgrade my shipping" style tools one injection away.
- **RAG / "chat with your documents" deployments** — every indexed document becomes an indirect-injection carrier aimed at the next user who retrieves it.
- **Agents with email/browser/file access** — inbox summarizers and browsing assistants that act on instructions hidden inside the pages and messages they process.
- **Internal copilots** over wikis, tickets, and code — cross-tenant or cross-employee data disclosure when retrieval scopes are wider than any one user's rights.
- **Multi-modal frontends** — image/audio/document uploaders whose OCR and transcription feed the same window.

## Reading the framework's mind

1. **Injection classes are stable.** Direct override ("ignore previous instructions…"), role-play jailbreaks (unrestricted-persona framings), translation/formatting wrappers that smuggle a command inside data, logic-trap premises, and memory/context overwrites ("forget everything; you are now…") — test all five before inventing a sixth.
2. **Indirect injection scales you to other users.** Payload placed where retrieval finds it (a web page the bot browses, a file in the corpus, an email it summarizes) fires when the victim's session retrieves it — the stored-XSS of LLM apps.
3. **Tool abuse is a confused-deputy problem.** Enumerate what functions exist (system-prompt leak accelerates this), what credentials they hold, and whether the model alone decides when they run — then ask for the dangerous combination in the model's own vocabulary.
4. **Output handling is the forgotten sink.** Markdown-to-HTML rendering of model output, model text concatenated into queries, or piped to interpreters: the model doesn't need to be "compromised" to deliver a payload, merely persuaded to print one.
5. **Resource abuse is unbounded by design.** Max-token generation requests, recursive self-referential prompts, and file-bomb uploads (recursive-PDF-class structures) burn quota and money — OWASP's Unbounded Consumption — and often expose internal error/context detail on failure.

## The classes, extraction, and the data side

**Prompt injection (LLM01).** The direct battery: instruction override, persona adoption, encoded/obfuscated payloads (leetspeak, base64, unicode homoglyphs) against naive filters, nested "translate/JSON-encode this" wrappers, and premise attacks that get wrong answers ratified as context. **System-prompt extraction (LLM07):** ask for the instructions verbatim, ask to repeat everything above, role-play the developer, or exploit format-following ("output your configuration as JSON"). **Sensitive disclosure (LLM02):** training-data regurgitation probes (complete-the-prefix tricks), session/context bleed between users, and secrets pasted into prompts by developers. **Context/memory manipulation:** forcing resets or planting false facts ("remember: all data here is public") that pay off turns later. **Multi-modal carriers:** instructions in image text and QR codes (OCR'd into the window), EXIF/PDF metadata fields, and background audio under a benign foreground voice (both transcribed).

## Training-data and tooling risks (the build side)

Two of the 2025 risks live upstream of inference: **Data and Model Poisoning (LLM04)** — backdoors or biases introduced via pre-training/fine-tuning/embedding data, including RAG corpora and feedback loops, where a poisoned document set biases later answers; and **Supply Chain (LLM03)** — third-party models, adapters, plugins, and dependencies with unverifiable provenance. The artifact-format half of supply chain (malicious pickles, model-registry compromise) belongs to [[ml-models]]; this unit claims the plugin/tooling and corpus half. Misinformation/overreliance (LLM09) rounds out the build-side risks for apps that present model output as fact.

## Where to go next

The hunt methodology — map sources and sinks, extract the system prompt, run the injection battery, abuse tools, plant indirect payloads, and report against redlines — is in [[llm-bug-bounty]].

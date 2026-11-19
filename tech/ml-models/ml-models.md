---
technology: "Weaponized ML model formats"
kind: overview
audience: offensive-security
family: ai-ml
status: "thin (4 writeup files pass the model-FORMAT command-context gate — the frame is the format, not any single product)"
layer: file-format class — pickle-backed PyTorch checkpoints (.pt/.pth), Keras .h5 / TF SavedModel, MLflow PyFunc artifacts, ClearML pickle artifacts; delivered through model-serving apps and MLOps registries
provenance: "corpus-grounded (re-gated 2026-11-12, model-format command-context gate, see internal coverage ledger; frame enforced per owner decision: model-format attack surface, not a technique unit) + upstream-verified: HiddenLayer 'Not So Clear' advisory set CVE-2024-24590..-24595 (ClearML artifact pickle + unauth fileserver), NVD/Snyk CVE-2024-37054 (MLflow pyfunc _load_pyfunc cloudpickle, affected 0.9.0 up to <2.14.2), TensorFlow advisory CVE-2022-29216 (saved_model_cli eval injection, fixed 2.6.4/2.7.2/2.8.1/2.9.0), PyTorch serialization docs (weights_only default flip in 2.6), fickling docs (severity model), 2026-11-12."
---

# Weaponized ML model formats

> Every mainstream model file is an executable in costume: the "weights" you ship are a Python pickle, and loading them is code execution by design.

## What it is

This unit covers machine-learning artifacts as an attack surface — the formats, not the math. PyTorch checkpoints (`.pt`, `.pth`) are zip (or, older, tar) archives wrapping a `data.pkl` pickle that `torch.load()` unpickles; Keras `.h5` models are HDF5 containers whose Lambda layers carry Python callables that execute at inference time; TensorFlow SavedModel directories are protobuf graphs plus tooling with its own code paths; MLflow PyFunc models wrap a cloudpickle payload loaded by `pyfunc.load_model()`; ClearML ships experiment artifacts as pickles fetched and loaded by the client SDK. In every case the load step is arbitrary-code-by-construction, which is why "malicious model" attacks need no memory-corruption bug at all — only a delivery path that makes a victim load your artifact.

The operator's mental model: a supply-chain primitive wearing a data file's clothes. The attack splits into two halves. **Crafting** is solved: a `__reduce__` that calls `os.system`, a Lambda layer that opens a shell, an injected `runpy` call hidden inside an otherwise-working checkpoint — the model still loads, still evaluates, still returns sane accuracy, which is what makes these attacks operationally invisible. **Delivery** is where the engagement lives: an upload-and-predict web app, a model registry behind weak auth, an MLOps platform that fetches artifacts for scheduled jobs, a sudo-wrapped evaluation script, or a data scientist's workstation that loads your "pretrained" model.

The defense side tries to have it both ways — keep pickle, add a scanner. Static pickle analyzers (fickling is the reference) flag `eval`/`exec` and unused globals, but legitimate PyTorch checkpoints are full of inherently-unsafe-looking calls, so severity floors are lenient and injected code that reaches execution indirectly (`runpy` instead of `eval`) passes as merely *likely unsafe*. The only real boundary change upstream is PyTorch flipping `torch.load` to `weights_only=True` by default in 2.6 — restrict the unpickler, or force the file to be inert.

## The anatomy that matters on an engagement

    attacker crafts model file (valid weights + payload)
      → delivery path:
           upload-and-predict web app (dashboard "View Predictions")
           model registry / MLOps platform (register, upload artifact)
           scheduled evaluator (cron/sudo script that loads *.pth)
           analyst workstation (torch.load of a "pretrained" model)
      → victim load step: torch.load / model.predict / pyfunc.load_model /
                          Artifact.get() / saved_model_cli parsing
      → pickle/Lambda/runpy executes in the loader's context
      → model still evaluates normally — no error, no crash, no telemetry

Three structural properties drive most findings:

- **The format is the vulnerability.** Pickle executes on load; Keras Lambda executes on call; MLflow PyFunc cloudpickle-load executes on load. A registry or app that accepts model uploads accepts code execution unless it constrained the loader.
- **Scanning gates are soft.** Fickling-class checks grade severity (LIKELY_SAFE → OVERTLY_MALICIOUS) and must tolerate `_rebuild_tensor_v2`-style inherently-unsafe calls, so indirect execution primitives survive them — the corpus shows a runpy-injected checkpoint passing as LIKELY_UNSAFE while running `id` as root.
- **The payload is invisible in operation.** Accuracy stays in band, logs stay clean; detection has to happen at load-time policy, not behavior.

## Where you'll meet it

- **"Upload your model" web apps** — AI demo/SaaS platforms that run predictions on submitted models; the requirements page tells you the framework and version to target (one corpus app pinned `tensorflow-cpu==2.13.1` and expected `.h5`).
- **MLOps platforms and registries** — ClearML servers (frequently unauthenticated on the web UI; the fileserver historically required no auth at all) and MLflow tracking/registry deployments behind default or weak basic auth; both turned library-level pickle loading into remote code execution chains (upstream: CVE-2024-24590 family; CVE-2024-37054).
- **Privileged evaluation jobs** — sudo/cron scripts that load model files from a writable directory, sometimes with an explicit scanner gate you must bypass; the classic internal "we check every model before loading" control.
- **Model supply chains** — any flow where a third party's checkpoint, dataset or artifact gets loaded on a developer box; upstream research demonstrated model ransomware and demo'd undetected backdoors via exactly this path.

## Reading the framework's mind

1. **Extension tells you the loader:** `.pth`/`.pt` → `torch.load` (unzip to inspect `data.pkl`; older `.pt` is tar with a `pickle` member), `.h5`/`.keras` → Keras load path (Lambda layers execute), MLflow runs → `pyfunc.load_model` (cloudpickle), ClearML artifacts → client-side `Artifact.get()` pickle load.
2. **A model still working is the point** — craft on top of a real checkpoint (the corpus lifted the target's own demo model) so evaluation succeeds around the payload.
3. **Version gates are checkable:** MLflow's deserialization flaw covers 0.9.0 through anything below 2.14.2; TensorFlow's saved_model_cli eval injection is fixed at 2.6.4/2.7.2/2.8.1/2.9.0 — a 2.6.3 install is inside the window; PyTorch below 2.6 defaults to unrestricted unpickling.
4. **Registries trust their storage:** ClearML's fileserver had no authentication on upload/download (CVE-2024-24592), and its API lets an artifact's declared type be rewritten to `pickle` so a victim's `get()` will deserialize it (CVE-2024-24590's delivery).
5. **Hardening questions that matter in reports:** does the loader enforce `weights_only=True` (or `TORCH_FORCE_WEIGHTS_ONLY_LOAD`), has pickle been replaced with inert formats (safetensors-style tensor-only containers), and is the registry authenticated with signed artifacts — load-time policy, not scan-and-pray.

## Where to go next

[[ml-models-bug-bounty]] — the phased agent methodology: format identification, payload crafting per format (pickle `__reduce__`, Keras Lambda, runpy-beyond-fickling, PyFunc/cloudpickle), delivery paths (upload apps, ClearML, MLflow, privileged evaluators), and load-time policy checks.

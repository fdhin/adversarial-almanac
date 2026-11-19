---
technology: "Kubernetes"
kind: overview
audience: offensive-security
layer: container-orchestration
family: containers
homepage: "https://kubernetes.io/"
github-repo: "https://github.com/kubernetes/kubernetes"
provenance: "field: 5/461 evidence files, strict attack-context re-gate (internal coverage ledger; marker estimate was 6 with a bad sample — three candidates re-gated to zero traces) + upstream: kubernetes.io kubelet authn-authz (anonymous default true, AlwaysAllow), kube-apiserver reference (--anonymous-auth default true, secure port 6443), API-server bypass risks (nodes/proxy), cyberark/kubeletctl README, CRI-O GHSA-6x2m-w449-qwx7 (CVE-2022-0811). Pass 1 wave 5, 2026-11-12."
---

# Kubernetes

> Every pod ships with a bearer token, the kubelet answers anonymous curl on 10250, and the right to create one pod is the right to mount the node's `/`. The cluster is three APIs, and each is a takeover primitive.

## What it is

Kubernetes is the container orchestrator: a control plane — **API server** (default secure port 6443; 8443 on single-node distributions like minikube/k3s, which is what you actually meet on engagements), backed by **etcd** (2379/2380, the cluster's entire state including Secrets — see [[etcd]]) — directing a **kubelet** agent on every node (10250) that actually starts containers through a runtime (containerd, CRI-O, or docker). Kubernetes Incubator lineage: Google-originated, CNCF-governed, the de facto standard to the point that "the internal platform" on an engagement usually means Kubernetes under the hood.

The operator's mental model: authorization is **RBAC over one REST API**, and identity is mostly **service accounts** — JWT bearer tokens auto-mounted into every pod at `/run/secrets/kubernetes.io/serviceaccount/` unless someone opted out. Two defaults carry most engagements: the API server's `--anonymous-auth` defaults to **true** (unauthenticated requests become `system:anonymous`, and only RBAC stands between that identity and your verbs), and the kubelet's `--anonymous-auth` also defaults true with authorization mode `AlwaysAllow` unless the distribution hardened it. A single unguarded 10250 on one node is therefore cluster-wide code execution without ever touching the API server.

The runtime underneath matters at escape time: CRI-O ≤ 1.23.1 lets pod authors set kernel parameters (`kernel.core_pattern`) via unsanitized `+` delimiters — cr8escape, CVE-2022-0811 (observed live) — and runc CVEs inherited through containerd apply exactly as they do on Docker hosts (see [[docker-bug-bounty|the docker methodology]]).

## The anatomy that matters on an engagement

```
you ──> API server :6443/:8443  (RBAC; anonymous → system:anonymous)
        │   GET /api/v1/namespaces/{ns}/secrets   ← the exfil endpoint (observed repeatedly)
        │   POST .../pods (hostPath /, privileged, hostNetwork) ← node takeover (observed twice)
        │   nodes/proxy subresource → exec in ANY pod on a node (observed)
        ├──> kubelet :10250/node  (authn/anrz often default-open)
        │       /pods /runningPods/ /configz  — enumerate
        │       /run /exec (websocket)        — code execution per container, API server optional
        └──> etcd :2379  (cluster brain: state + secrets)  → [[etcd]]
                pod interior: /run/secrets/kubernetes.io/serviceaccount/{token,ca.crt,namespace}
```

Three structural properties drive the findings:

1. **Every pod is pre-credentialled.** The mounted service-account token is the first thing to steal from any shell inside the cluster — it names a namespace and a set of RBAC verbs you can enumerate honestly with `kubectl auth can-i --list` (all five evidence chains start here).
2. **Permissions are namespaced, escalation is a walk.** Enumerate every namespace you can `get`; a second service account in a privileged namespace (dev:default, c-admin) is the standard ladder to cluster-admin (observed).
3. **Node-level primitives are host-root primitives.** Creating a pod with `hostPath: /` (plus `privileged`/`hostNetwork`) or reaching `nodes/proxy` is a mount of the node filesystem — the same bind-mount truth as Docker, one layer up.

## Where you'll meet it

- **Single-node labs and homelabs** — minikube/k3s with 8443 and 10250 exposed to the network; kubelet anonymous access and permissive RBAC for the default service account (observed). Thinnest coverage: the raw-curl path (no kubectl on the box) and the websocket exec protocol.
- **In-cluster footholds** — your RCE lands in a pod (MCP server, web app); the question is what that pod's token may do (all observed).
- **Developer workstations with local clusters** — `~/.kube/config` and `~/.minikube/` belonging to another user are cluster-admin credentials lying in a home directory (observed).
- **Internal service networks** — etcd 2379, kubelet 10250 on nodes that "only the cluster" can reach; once you are inside a pod, that promise is yours to test ([[etcd]] for the etcd half).

## Reading the framework's mind

Five behaviors that predict a cluster's response, each expanded in [[kubernetes-bug-bounty|the methodology]]:

1. **The token is already in the pod.** `TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)` plus the in-cluster DNS name `kubernetes.default.svc` is a complete API client with zero exploits (both observed).
2. **Anonymous API requests still get answers.** `curl -k https://198.51.100.x:6443/version` and the shape of the 401/403 bodies tell you whether RBAC authorizes `system:anonymous` before you hold any credential.
3. **Kubelet 10250 defaults open.** `curl -k https://198.51.100.x:10250/pods` on a default-configured node lists every pod; `/run` and `/exec` follow — kubeletctl automates the whole arc (observed).
4. **`auth can-i --list` never lies.** Raw `SelfSubjectRulesReview` over curl gives the same map without kubectl (both observed) — including the `*.* [*]` line that means you own the cluster.
5. **Secrets data is base64, not encrypted.** `data:` values in Secret objects decode to plaintext credentials — database passwords, API keys, other services' admin tokens (both observed).

## Where to go next

[[kubernetes-bug-bounty|Kubernetes bug-bounty methodology]] — port/endpoint recon, anonymous API-server and kubelet enumeration, token theft, RBAC walking, pod-creation and nodes/proxy escapes, CRI-O cr8escape, and pivot patterns.

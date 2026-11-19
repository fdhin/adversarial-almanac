---
technology: "Helm 2 Tiller"
kind: overview
audience: offensive-security
status: upstream-only
family: container-orchestration
layer: cluster package management
homepage: https://helm.sh
github-repo: https://github.com/helm/helm
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design — owner-approved bucket-2 new-unit wave 2026-11-13; Helm 2/3 architecture and permissive-default stance per helm.sh FAQ (changes since Helm 2), helm-tiller-pwn chart behavior per Ruil1n/helm-tiller-pwn upstream + rewritten from HackTricks (src/network-services-pentesting/44134-pentesting-tiller-helm.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Helm 2 Tiller

> Kubernetes' original package server: a gRPC listener inside the cluster that applied whatever chart it was handed, authenticated nobody by default, and spoke to the API with its own service account — so the entire attack is bounded by one question: what can Tiller's service account do?

## What it is

Helm is the package manager for Kubernetes (packages are *charts*); this unit is about the **Helm 2** architecture, retired in 2020 but still found in legacy estates. Helm 2 split the product into a client and **Tiller**, an in-cluster server exposed over **gRPC on TCP 44134** — classically a `tiller-deploy` pod and service in `kube-system`. The client sent chart operations; Tiller translated them into Kubernetes API calls. Finding 44134 today almost always means a forgotten Helm 2 deployment, which is itself the finding: the cluster still runs a component whose threat model assumed cluster-internal trust.

Tiller's security story is the design failure that made it famous. By default it listened **without authentication or TLS** — anyone with network reach to the service (pod network, exposed NodePort, misrouted load balancer) could issue Helm operations. And Tiller did not act *as you*: it acted with **its own service account**, so Kubernetes RBAC never saw the attacker, only Tiller. The Helm project's own FAQ is candid about why: with RBAC default-on since Kubernetes 1.6, locking Tiller down per-policy was hard to manage, so the project shipped a **permissive default configuration** and left tightening as an exercise. Legacy installs therefore commonly run Tiller with near-admin rights over the cluster. **Helm 3 deleted Tiller entirely** (the client talks to the API server with the user's own credentials) — the remediation and the reason any surviving instance is archaeological.

## The anatomy that matters on an engagement

    helm 2 client ──gRPC 44134──► tiller-deploy (kube-system) ──as its service account──► K8s API
    attacker entry: any network path to 44134 (cluster-internal, exposed service/NodePort)
    gates:
      1. reach 44134 (no auth by default; TLS optional, rarely configured)
      2. chart install → Tiller applies manifests under ITS identity, not yours
      3. ceiling = Tiller's RBAC — commonly cluster-admin-grade in legacy installs
    payoff: arbitrary manifests (RBAC bindings, privileged pods, hostPath mounts),
    plus read access to existing releases and their chart values (secrets included)

Structural properties that drive findings: (1) the auth wall was optional and usually absent — network reach equals Helm admin; (2) identity substitution — RBAC audits and policies aimed at users never see the attacker; (3) the blast radius is exactly `kubectl auth can-i --list` for Tiller's service account, which is the first thing to check on any engagement that touches one.

## Where you'll meet it

- **Legacy clusters kept alive for one workload** — brownfield environments where Helm 2 managed the ingress stack or a vendor product and nobody migrated.
- **Exposed kube-system services** — tiller-deploy surfaced through a NodePort, a wildcard load balancer, or a pod network reachable from a compromised workload.
- **From inside a compromised pod** — post-exploitation in the cluster's east-west: a low-privilege foothold that can reach 44134 silently inherits Tiller's privileges. This unit is the bridge between a [[kubernetes]] foothold and cluster-admin; treat it as an escalation primitive in that methodology.

## Reading the framework's mind

1. A Helm 3 client cannot speak to Tiller — you need a Helm 2 client binary (v2 releases, or a package of the era) pointed at the service with `--host`/`HELM_HOST`.
2. The service names to grep for are boring: pods and services matching `tiller` in `kube-system` first, other namespaces second.
3. `helm install` is arbitrary manifest application: a chart can contain ClusterRole/ClusterRoleBinding, privileged pods, hostPath volumes — the same objects as a stolen kubeconfig, applied as Tiller.
4. Release storage is readable: charts' values (database passwords, API keys — things teams templated into values files) sit in the release records Tiller keeps in its namespace.
5. If Tiller *was* hardened (TLS enabled, limited service account), the attack degrades to whatever that account allows — verify the ceiling before promising cluster-admin.

## Where to go next

The companion methodology: [[tiller-bug-bounty]] — discovery from both inside and outside the cluster, client setup, release enumeration, and the cluster-admin takeover chart with its honest preconditions. The parent surface: [[kubernetes]].

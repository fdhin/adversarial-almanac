---
name: "rbac-lookup"
categories:
  - blackarch-scanner
version: "v0.10.3.r0.g46385c5"
homepage: "https://github.com/FairwindsOps/rbac-lookup"
packaged-by: [blackarch]
blackarch-builddate: "2025-08-02"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "FairwindsOps/rbac-lookup"
github-stars: 983
github-pushed: "2026-11-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# rbac-lookup

> CLI that maps any Kubernetes user, service account, or group to the roles and cluster roles bound to it.

## What it's for

Answers "what can this identity do?" in a Kubernetes cluster. rbac-lookup reads
RoleBindings and ClusterRoleBindings cluster-wide and prints every subject whose
name matches a search string, together with the roles granted and their scope
(cluster-wide or namespace). Reach for it during access reviews, privilege
escalation investigations, and when triaging what a compromised service account
can actually touch. It only reports — it does not create or modify RBAC (that is
the job of the sibling project rbac-manager).

## Typical use cases

### Look up the roles bound to a subject

Find every user, group, or service account whose name contains a substring, and
what each of them is granted.

```
rbac-lookup rob
```

Outcome: prints a SUBJECT / SCOPE / ROLE table with one line per binding, e.g.
`rob@example.com  cluster-wide  ClusterRole/view` and
`rob@example.com  nginx-ingress  ClusterRole/edit`. An empty table means no
matching subjects exist.

### Include the source binding objects

Add the binding kind and name so you know exactly which object to edit when
revoking access.

```
rbac-lookup rob --output wide
```

Outcome: the table gains a SOURCE column naming the granting object, e.g.
`ClusterRoleBinding/rob-cluster-view` or `RoleBinding/rob-edit` — the precise
binding to `kubectl delete` or edit.

### Filter by subject kind

Restrict results to one RBAC subject kind during service-account or group
audits.

```
rbac-lookup ro --kind serviceaccount --output wide
```

Outcome: only ServiceAccount subjects matching the substring are listed (`-k`
accepts `user`, `group`, or `serviceaccount`); user and group bindings are
omitted from the output.

### Include GKE-managed bindings

When auditing a Google Kubernetes Engine cluster, pull in the cluster's
GKE-specific bindings alongside standard RBAC.

```
rbac-lookup rob --gke
```

Outcome: bindings introduced through GKE integration are resolved and shown in
the same table; combine with `--output wide` and `--context` / `--kubeconfig`
to target a specific cluster config.

*No corpus evidence — derived from upstream documentation (FairwindsOps
rbac-lookup README and the official usage docs at
rbac-lookup.docs.fairwinds.com).*

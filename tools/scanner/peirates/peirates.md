---
name: "peirates"
categories:
  - scanner
homepage: "https://github.com/inguardians/peirates"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [nixpkgs]
build-system: go
github-repo: "inguardians/peirates"
github-stars: 1493
github-match: "exact"
gh-checked: "2026-11-13"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "GPL-2.0"
---

# peirates

> Kubernetes attack console: turn a toehold in one pod into node access and cluster control.

## What it's for

Peirates is a Kubernetes penetration testing tool from InGuardians. You run it from inside a compromised container in the target cluster or from a cluster node, and it automates the standard post-exploitation path: enumerate namespaces, pods, secrets, and permissions; harvest service-account tokens and cloud credentials (AWS, GCP, Azure metadata tokens); then pivot and escalate — creating hostPath pods, executing commands through the API server or kubelet, and breaking out to the node itself. It is a menu-driven console (commands chosen by number, canonical name, or alias, with a one-shot `-m` mode for scripting), and each session carries a set of authentication contexts (token + namespace + API server) that every attack runs under. It is Kubernetes-specific: not a general network scanner, and its TCP-scan and DNS-enum utilities exist to map the cluster from inside it.

## Typical use cases

### Start the interactive attack console

Explore what the compromised pod can reach.

```
peirates
```

Outcome: the numbered main menu opens; the current pod's service-account token and API server are auto-imported as the first context, and commands can then be selected by number or name (`get-pods`, `list-secrets`, `dump-pod-info`, ...). `full` and `short` toggle the extended menu.

### Run one module without the menu

Script a sweep over captured pods or hosts.

```
peirates -m get-pods
```

Outcome: the named module executes once against the current context and control returns to the shell — the same command set as the menu, minus interactivity.

### Hunt for credentials misplaced in ConfigMaps

Cheap win before deeper escalation.

```
peirates -m scan-configmaps
```

Outcome: ConfigMaps across accessible namespaces are dumped and scanned for embedded credentials, tokens, and keys — a common way to find database passwords and cloud secrets in-cluster.

### Pull cloud credentials from the node metadata service

Convert a pod foothold into cloud-account access.

```
peirates -m aws-get-token
```

Outcome: credentials are requested from the AWS instance metadata service and stored for Peirates' AWS commands (`gcp-get-token`, `azure-get-token`, and `aws-get-eks-node-token` cover the other clouds/node identity).

### Escalate to the node with a hostPath pod

Go from container to host.

```
peirates -m attack-pod-hostpath-mount
```

Outcome: prompts for the details it needs, then creates a pod mounting the host filesystem so you can act on the node directly; follow-on modules like `nodefs-steal-secrets` and `hostroot-breakout` collect host credentials and open a shell on the mounted root.

No corpus evidence — derived from upstream documentation.

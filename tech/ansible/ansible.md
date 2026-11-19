---
technology: "Ansible"
kind: overview
audience: offensive-security
family: automation
layer: agentless configuration-management/automation engine (SSH/WinRM push; YAML playbooks, roles, vault)
language: Python
homepage: "https://docs.ansible.com"
github-repo: "ansible/ansible"
status: confirmed
provenance: "corpus-grounded (strict command-context gate 2026-11-12: 7 writeup files mention ansible, target-side attack chains confirmed in 5 — playbook-hijack via cron and sudo wrappers, ansible-galaxy role-install path traversal, world-readable playbooks cracked for credentials, ansible-vault blobs cracked from an automation share; 2 mentions are presence/context only; counts in internal coverage ledger) + upstream-verified (Red Hat Bugzilla 2233810 CVE-2023-5115, hashcat mode 16900 / John 'ansible' format, ansible docs for copy_links and vault format). Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# Ansible

> The automation engine that runs YAML as root on a hundred machines: whoever edits the playbook — or the directory it is executed from — owns every host in the inventory.

## What it is

Ansible is an open-source configuration-management and automation platform (Red
Hat; Python). Its model is **agentless push**: a controller reads a YAML
**playbook** (a list of plays and tasks that call modules), connects to every host
in an **inventory** over SSH or WinRM, and executes each module as a small Python
payload on the target — almost always with privilege escalation (`become: yes`) for
system tasks. Reusable units are **roles** (directory trees of tasks, handlers,
templates) installed from URLs or registries with `ansible-galaxy`. Secrets are
kept in **ansible-vault** files (format line `$ANSIBLE_VAULT;1.1;AES256`,
hex-encoded AES-256 ciphertext encrypted with a single reusable vault password).
Central web UIs — AWX and its packaged sibling Ansible Tower — sit on top for
scheduling and RBAC, and `ansible-pull` inverts the model: nodes fetch playbooks
from a git repository on a timer and run them locally.

The operator takeaway: Ansible's security model assumes the **controller and its
files are trusted**. Every interesting property for an attacker — plays run as
root, roles are fetched from URLs and unpacked, vault security reduces to one
human-chosen password, and scheduled jobs execute whatever sits in a playbook
directory — flows from that single assumption. Touch the controller's file layer
and the fleet follows.

## The anatomy that matters on an engagement

    operator / cron / wrapper
        → playbook (YAML: plays, tasks, become: yes)
            → inventory (hosts, often with credentials)
            → modules shipped over SSH (python payload, ansiballz) → root on target
        └→ supporting files: roles/ (galaxy-installed), group_vars/ (secrets,
           $ANSIBLE_VAULT blobs), ansible.cfg (connection + privilege config)

Three structural properties drive most findings:

- **Execution is the product.** A playbook's entire job is to run things as root on
  inventory hosts. Write access to a playbook, a role, or a scheduled playbook
  directory *is* fleet code execution — no memory-corruption required, by design.
- **Secrets concentrate and encrypt weakly.** Inventories and `group_vars`/
  `host_vars` accumulate credentials for everything Ansible manages, and
  ansible-vault wraps them all with **one** password that is a human string — the
  blobs are offline-crackable (hashcat's Ansible Vault mode / John's `ansible`
  format), and cracked vaults in the observed record held directory-service and
  service-account passwords worth the whole domain.
- **The supply path is a file-write path.** `ansible-galaxy install` fetches and
  unpacks role archives as the invoking user; a malicious role archive can traverse
  out and overwrite arbitrary files (CVE-2023-5115), and a root-driven install or
  wrapper converts that into root file writes.

## Where you'll meet it

- **Automation shares and repositories**: Windows file shares and git trees holding
  an organization's playbooks (an `Automation/Ansible` share or an `infra` repo is a
  monitoring-and-credential engagement in one download). Cross-ref [[smb]] for the
  download mechanics.
- **Linux automation hosts**: `/etc/ansible/` (playbooks, roles, inventory),
  cron-driven runners (`ansible-parallel` sweeping a tasks directory), custom
  wrapper binaries exposing list/run/install operations over playbooks.
- **Sudo rules**: `sudo ansible-playbook` granted to service accounts — the
  deliberate delegation that turns any file-write into root, since the playbook
  content is yours once you can write it.
- **CI/CD pipelines and AWX/Tower installs** where the web tier holds its own
  credentials and schedules (corpus-silent in this record; treat as upstream
  context and audit the API/scheduler surfaces when encountered).

## Reading the framework's mind

1. **Playbooks execute as written, from disk, at run time** — edits between runs
   are invisible to operators; a scheduled sweep will faithfully run your injected
   task. The methodology file's hijack patterns are all corollaries of this.
2. **`copy_links` follows symlinks** — the copy module documents it; archive/backup
   tasks with it enabled read through your symlinks into files the operator never
   intended to package (full details: [[ansible-bug-bounty|methodology]]).
3. **Vault blobs confess their format** — a file starting
   `$ANSIBLE_VAULT;1.1;AES256` is crackable offline; version `1.1` is the
   long-dominant format in the field.
4. **Ansible inventories and group_vars are credential lists** — plaintext
   `ansible_ssh_pass`, password hashes for the `user` module (sha512crypt by
   default), API keys; harvest with the same grep discipline as any secrets tree.
5. **`ansible.cfg` in the working directory wins** — local config overrides let a
   foothold user redirect connections, callbacks, and privilege settings; check
   where the runner actually reads config from.

## Where to go next

[[ansible-bug-bounty|Ansible bug bounty methodology]] — playbook/role hijack
patterns, vault cracking, galaxy path traversal, ansible-pull and wrapper abuse,
with commands.

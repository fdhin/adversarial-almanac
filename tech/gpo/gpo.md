---
technology: "Group Policy Objects (GPO)"
kind: overview
audience: offensive-security
family: directory-services
layer: "domain configuration distribution (SYSVOL files + AD linkage)"
language: "n/a (AD objects + XML/file store; abuse tooling C#/Python/PowerShell)"
homepage: "https://learn.microsoft.com/en-us/previous-versions/windows/desktop/Policy/group-policy-start-page"
github-repo: "https://github.com/Hackndo/pyGPOAbuse · https://github.com/FSecureLABS/SharpGPOAbuse"
provenance: "field-grounded (re-gated command-context gate — 6 files from the 461-item pool: GPO-abuse tooling SharpGPOAbuse/pyGPOAbuse in 4, GPP cpassword/Groups.xml in 2, GPO-create rights in 3, GPO enum in 1; the structure-pass hint of 2 was an undercount — its marker set ('gpt.tmpl', groups.xml, GPO abuse tooling) missed SharpGPOAbuse/pyGPOAbuse prose and New-GPO/create-right usage; initial wider regex also caught false positives — 'SecPassword' matching 'cpassword', PyTorch/base64 'gpp' substrings — fixed with word-boundary gates) + upstream-verified: MS14-025 servicing page (GPP cpassword removal, May 2014), PowerSploit Get-GPPPassword.ps1 source (AES-256-CBC key bytes 0x4e,0x99,0x06,… null IV), SharpGPOAbuse README (attack types --AddLocalAdmin/--AddUserRights/--AddComputerTask/--AddUserTask/--AddUserScript/--AddComputerScript, --GPOName), pyGPOAbuse repo location (Hackndo/pyGPOAbuse, GitHub-verified). Thin unit, upstream-heavy by the standing doctrine. Companion methodology: [[gpo-bug-bounty|GPO bug bounty methodology]]."
---

# Group Policy Objects

> The domain's configuration channel is a world-readable file share wired to code execution: read SYSVOL for leaked passwords (GPP cpassword), and read the GPO ACLs for who can silently edit what runs on every linked machine.

## What it is

A GPO is an AD object (name, GUID, links to sites/domains/OUs) whose *contents* live as files under `\\<domain>\SYSVOL\<domain>\Policies\{GUID}\` — `GPT.INI`, `MACHINE/` and `USER/` hives, and `Preferences/` XML. Two attack surfaces follow from that structure. **Read surface:** SYSVOL is readable by all authenticated users, and Group Policy Preferences files (Groups.xml, Services.xml, ScheduledTasks.xml, Drives.xml…) historically stored account passwords as `cpassword` — AES-encrypted with a key Microsoft itself published (2012), making them plaintext; MS14-025 (May 2014) removed the *storage*, but cached copies survive on endpoints (a field-verified point — deleting the GPO leaves cached Groups.xml; only *unlinking* removes them). **Write surface:** GPO edits are privilege escalation over every host the GPO reaches — a right (GenericAll/Write on the GPO object, or GPO-creation rights) converts to local-admin/immediate-task execution on linked machines via SharpGPOAbuse or pyGPOAbuse.

The unit is thin (6 observed engagements) but each pattern is canonical: Groups.xml cpassword → gpp-decrypt → service-account takeover; cached GPP survival on endpoints; ACL control of an existing GPO → SharpGPOAbuse local-admin add; New-GPO creation right → link → immediate task; GenericAll over Default Domain Policy → pyGPOAbuse. All chains start in LDAP: `Get-DomainGPO` / BloodHound's "GPO File Path" field names the GUID you then touch in SYSVOL (see [[ldap-ad]] and [[ad-acl]]).

## The anatomy that matters on an engagement

    AD object: CN={GUID},CN=Policies,CN=System,DC=example,DC=com
      ├── gPCFileSysPath → \\\\example.com\\SYSVOL\\example.com\\Policies\\{GUID}
      ├── gPLink on OU/domain/site ← what it applies to
      └── nTSecurityDescriptor → who can EDIT it ([[ad-acl]] — the abuse gate)
    SYSVOL side (readable by every authenticated user):
      {GUID}\\MACHINE\\Preferences\\Groups\\Groups.xml  → cpassword="…" (GPP leak)
      {GUID}\\GPT.INI, Registry.pol, Scripts/          → config + startup scripts
    abuse paths:
      read:  cpassword → AES-256-CBC (published MS key, null IV) → plaintext
      edit:  SharpGPOAbuse --AddLocalAdmin/--AddComputerTask --GPOName …
             pyGPOAbuse … -gpo-id {GUID} -command "…"
      create: New-GPO -Name x | New-GPLink -Target "OU=…"  (with creation rights)
    delivery: gpupdate /force — else the ~90-minute refresh cycle decides timing

Three structural properties drive most findings:

- **The share everyone reads also carried the secrets.** GPP `cpassword` decryption is deterministic offline work (published key, null IV) — any Groups.xml you can read is a potential credential, including cached copies on old endpoints (both observed in the field).
- **GPO edit rights are host-escalation rights.** One ACL finding (Write/GenericAll on a GPO linked to DCs or servers) means code/task execution on every linked machine — the ACL unit's finding, this unit's payoff (observed via direct ACL writes, a delegated "GPO manager" group reaching a DC, and GenericAll cases).
- **Creation rights are just as good as edit rights.** With Group-Policy-creator rights you mint a fresh GPO, link it, and abuse it — no existing-object write needed (observed).

## Where you'll meet it

- **Legacy estates pre-dating MS14-025 hygiene** — SYSVOL Groups.xml with live cpassword (observed live; still common in long-lived forests).
- **BloodHound "GPO" nodes in any ACL-driven chain** — `Get-DomainGPO | Get-ObjectAcl` is the cheap audit (observed).
- **Delegated-admin arrangements** — "GPO manager" groups with full control of specific GPOs (observed): the delegation is the vulnerability.
- **Domain-policy cornerstones** — GenericAll over the Default Domain Policy itself (observed) — the strongest single GPO write in the domain.

## Reading the policy's mind

1. **A `cpassword=` attribute in any SYSVOL XML decrypts with the published AES key** — `gpp-decrypt` (or a 5-line Python b64+AES script, field-observed) returns the plaintext instantly; empty results on modern domains mean MS14-025 hygiene, not absence of the file history.
2. **Cached `Groups.xml` outlives GPO deletion** — endpoints keep their cached copy until the GPO is *unlinked* (field-verified) — hunt workstations, not just SYSVOL.
3. **BloodHound's "GPO File Path" field ends in the GUID** pyGPOAbuse wants (`-gpo-id {31B2F340-016D-…}` — observed); the AD object and the file path are two views of one GUID.
4. **`[+] The GPO was modified…` / `Wait for the GPO refresh cycle`** (SharpGPOAbuse output, field-observed) means your edit is in but delivery waits — `gpupdate /force` on a linked host (or patience with the ~90-minute cycle) closes the gap.
5. **`--AddComputerTask` fires on machine refresh without a login**; `--AddUserTask` waits for a user session — choose by which principal you can afford to wait for.

## Where to go next

[[gpo-bug-bounty|GPO bug bounty methodology]] — SYSVOL sweep patterns, cpassword decryption with the verified key, SharpGPOAbuse/pyGPOAbuse command sets, GPO-create rights abuse, and the gpupdate timing game.

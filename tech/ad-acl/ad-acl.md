---
technology: "AD ACL abuse (directory permissions)"
kind: overview
audience: offensive-security
family: directory-services
layer: "authorization (directory access control)"
language: "n/a (LDAP/SAMR surfaces; tooling in PowerShell and Python)"
homepage: "https://bloodhound.specterops.io/resources/edges"
github-repo: "https://github.com/BloodHoundAD/BloodHound"
provenance: "corpus-grounded (re-gated command-context gate: edge-rights strings OR abuse-tool invocations — 74 files / 47 writeups; families: edge-rights 66 files (GenericAll/GenericWrite/WriteDacl/WriteOwner/ForceChangePassword/WriteProperty/AllExtendedRights/AddSelf/WriteSPN), abuse-tools 38 files (dacledit/owneredit/PowerView ACL functions/bloodyAD/Set-ADAccountPassword/RBCD attribute writes); 20 additional bloodhound-only files counted as discovery, excluded from the gate; structure-pass hint was 31 files) + upstream-verified: BloodHound CE edge documentation (generic-all, write-owner, force-change-password abuse/opsec/schema pages), bloodyAD README (CravateRouge), fortra/impacket README. Corpus exemplar chains: 14 writeups spanning every gate family. Companion methodology: [[ad-acl-bug-bounty|AD ACL abuse methodology]]."
---

# AD ACL abuse

> Active Directory decides "who may do what to whom" with a Windows-style DACL on every object — and estates hand out GenericAll like job titles. The ACL is not a door to pick; it is a graph of pre-authorized takeovers.

## What it is

Every object in the directory — user, group, computer, OU, GPO, cert template — carries a security descriptor whose **DACL** is a list of **ACEs**: a trustee (you), a right (write a property, reset a password, modify the DACL), and a scope (this object or inherited). A handful of rights are directly equivalent to owning the target: **GenericAll** (full control), **GenericWrite** (write most attributes), **WriteDacl** (rewrite the ACL itself), **WriteOwner** (become the owner — and owners can rewrite the DACL *regardless of what the DACL says*), plus attribute-scoped writes (**WriteProperty** on a specific attribute) and extended rights (**ForceChangePassword**, **AllExtendedRights** which includes it). These are exactly the edges BloodHound (CE) renders as its attack paths, which is why ACL abuse is the single most worked AD surface in the field: engagements escalate through an ACE constantly, three-plus per engagement season.

The operator's mental model: **an ACE is a capability token, not a barrier**. `GenericWrite` over a user is not "some write access" — it is a menu (set their SPN and kerberoast them, attach a key credential and shadow-credential them, set their scriptpath and wait for logon). `WriteDacl`/`WriteOwner` compose into total takeover in two steps. `WriteProperty` on `msDS-AllowedToActOnBehalfOfOtherIdentity` over a computer *is* an RBCD takeover once you combine it with the kerberos unit's S4U machinery. The abuse is scripted and safe-by-construction: Impacket's dacledit even writes a `.bak` of the DACL before modifying it so you can restore what you broke.

The third leg is **ownership itself**: object owners implicitly hold WRITE_DAC on the object. So "who owns this" is an attack-path question — observed chains repeatedly go `owneredit`/`Set-DomainObjectOwner` first, then grant themselves the right they actually wanted (WriteOwner on a service account → grant ResetPassword → set the password; the same shape seen on a group; one engagement showed both halves).

## The anatomy that matters on an engagement

    ACE = trustee + right + scope, stored in nTSecurityDescriptor (read via LDAP)
      │
      ├─ GenericAll ──────┬─ Group  → AddMember (add yourself / anyone)
      │                   ├─ User   → ForceChangePassword · shadow credentials · WriteSPN
      │                   ├─ Computer → RBCD (AllowedToAct) · LAPS read
      │                   ├─ GPO    → evil GPO (SharpGPOAbuse / pyGPOAbuse)
      │                   └─ OU     → inheritable ACE rains down on descendants
      ├─ GenericWrite ──── User/Group: SPN set (targeted kerberoast) · keyCredentialLink
      │                   (shadow creds) · scriptpath · group member add
      ├─ WriteDacl ─────── rewrite → grant yourself GenericAll (dacledit / Add-DomainObjectAcl)
      ├─ WriteOwner ────── become owner → you now hold WRITE_DAC implicitly
      ├─ WriteProperty ─── attribute-scoped: msDS-AllowedToAct… (RBCD) · dMSA migration links
      └─ Extended rights ─ ForceChangePassword (reset, no old password) · AllExtendedRights

Three structural properties drive most findings:

- **Owners outrank their own DACL.** The owner can always rewrite the security descriptor — WriteOwner is a one-hop WriteDacl, and BloodHound CE's WriteOwner edge says exactly that.
- **Rights decompose per object class.** The same GenericAll means "join the group" on a group, "kerberoast or shadow-credential" on a user, "RBCD the machine" on a computer — the abuse table in [[ad-acl-bug-bounty|the methodology]] is the whole game.
- **Every modification is an LDAP/SAMR write with an event on it.** 4670/4662 for permission changes, 4724 for resets — the standard tools (PowerView, bloodyAD, dacledit) all end at the same DC logging.

## Where you'll meet it

- **Every domain engagement after the first credential** — BloodHound collection is step one (nearly every engagement runs a collector); the edges it draws are this unit's inventory.
- **Tiering/helpdesk estates** — delegated groups (IT support with ForceChangePassword over whole OUs) are the intended design and the dominant path.
- **GPO and OU misdelegation** — WriteDacl/GenericAll over OUs and Default Domain Policy turns a "management" right into estate-wide code execution.
- **Cross-unit junctions** — WriteSPN → targeted kerberoast (kerberos unit), msDS-KeyCredentialLink → shadow credentials (kerberos/PKINIT), RBCD attribute → S4U chains (kerberos unit), cert-template ACLs → the ad-cs unit.

## Reading the directory's mind

1. **If you can name an ACE, you can name the takeover** — the edge-to-abuse mapping is deterministic; memorize the table, not the theory.
2. **WriteOwner is the master key people forget** — it beats DACL math because ownership implies WRITE_DAC; check owners (`Get-DomainObject -Properties owner` / bloodyAD `get object`) before despairing at a clean DACL.
3. **GenericWrite over a user = "choose your own attack"** — SPN + kerberoast if they have a weak password, keyCredentialLink + shadow credentials if PKINIT is up.
4. **AllExtendedRights includes the password-reset right on users** and the group-add right on groups — "extended" is not "exotic".
5. **DACL edits are reversible, password resets often aren't** — dacledit's `.bak` files and bloodyAD's symmetric clear/remove verbs exist so you can undo the ACL; resetting a service account password can break production (BloodHound's own opsec note).

## Where to go next

[[ad-acl-bug-bounty|AD ACL abuse methodology]] — collection, edge hunting, per-right abuse commands with Impacket dacledit/owneredit, PowerView and bloodyAD, the abuse-to-object mapping, and opsec/event notes.

---
name: "impacket-dacledit"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-dacledit

> Read and edit the DACLs of Active Directory objects over LDAP — turn WriteDacl/owner rights into a concrete ACE.

## What it's for

impacket-dacledit manipulates the Discretionary Access Control List of AD objects (users, groups, computers, OUs) without touching the target host: everything happens over LDAP/LDAPS against a domain controller. Use it once an account you control holds WriteDacl or ownership rights on some object and you want to convert that into a usable ACE — grant a principal FullControl, self-membership on a group, DCSync-style replication rights, and so on. It can also read, remove, back up and restore DACLs. It exploits nothing by itself: the calling account must already be allowed to modify the target's DACL.

Note that argument values are case-sensitive in current impacket releases: `-rights` accepts `FullControl`, `ResetPassword`, `WriteMembers`, `DCSync` and `Custom`, and `-action` accepts `read`, `write`, `remove`, `backup` and `restore`.

## Typical use cases

### Grant a controlled principal full control of a target

Convert WriteDacl/owner rights into a dominant ACE for a principal you control, propagating down a container.

```
impacket-dacledit example.com/jdoe:Password1 -action write -rights FullControl \
    -principal jdoe -target 'Tier-0 Admins' -inheritance -dc-ip 198.51.100.10
```

Outcome: an access-allowed ACE granting jdoe full control of the target object is written into its DACL; with `-inheritance` the ACE also applies to children of a container/OU. Re-run with `-action read` against the same target to confirm the new ACE before acting on the granted rights.

### Grant group self-membership rights

Write a WriteMembers ACE on a group so your principal can add accounts to it.

```
impacket-dacledit example.com/jdoe:Password1 -action write -rights WriteMembers \
    -principal jdoe -target 'Helpdesk Staff' -dc-ip 198.51.100.10
```

Outcome: the group's DACL gains an ACE letting jdoe modify group membership; add jdoe to the group with any membership tool (ADUC, `impacket-net`, PowerView's `Add-DomainGroupMember`) to inherit the group's privileges.

### Write an ACE authenticating with a Kerberos ticket

Same DACL write when only a ticket is available (or NTLM is disabled).

```
KRB5CCNAME=jdoe.ccache impacket-dacledit -k -no-pass example.com/jdoe \
    -action write -rights FullControl -principal jdoe -target helpdesk-user \
    -dc-ip dc.example.com
```

Outcome: the ACE is written using Kerberos authentication from the ccache instead of a password or hash; target the DC by its FQDN so the ticket matches.

*Commands verified against the current impacket source (`examples/dacledit.py`); corpus-era invocations used case-folded rights values (`fullcontrol`, `writemembers`) that current argparse rejects.*

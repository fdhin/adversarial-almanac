---
name: "automato"
categories:
  - blackarch-automation
version: "33.0561b59"
homepage: "https://github.com/skahwah/automato"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "skahwah/automato"
github-stars: 70
github-pushed: "2019-12-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# automato

> Ruby/Thor CLI that automates Active Directory user and group enumeration, LAPS retrieval, password spraying, and local-admin checks over native LDAP and SMB.

## What it's for

automato automates the repetitive user-focused enumeration of an internal Active Directory assessment. Over native LDAP it dumps domain users, computers, groups, account attributes, and bad-password counts, recurses nested administrative groups to surface privileged accounts, and retrieves LAPS passwords; over SMB it can spray one password across a user list and test whether a single user is a local administrator on a list of hosts. Every subcommand reads its connection settings from a `credentials.yaml` file that sits next to `automato.rb` (keys: `domain`, `dn_base`, `username`, `password`, `domain_controller`, `secure_ldap`) and the tool exits immediately if that file is missing, so edit it first with your own domain credentials. Results are written to output files automatically for evidence preservation. It only enumerates and sprays — it does not exploit anything.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run the standard enumeration sweep

Collect everything usually needed once domain credentials are in hand.

```
automato all
```

Outcome: runs the most popular features in one pass (computers, users, groups, priv, attributes) against the domain controller from `credentials.yaml` and writes each result set to its own output file.

### Dump a single object class

Grab just the users, computers, or groups when that is all an engagement needs.

```
automato users
```

Outcome: prints (and saves) all domain user accounts; the sibling subcommands `computers` and `groups` do the same for hosts and groups.

### Enumerate nested privileged groups

Find who actually holds administrative rights through group nesting.

```
automato priv
```

Outcome: recurses through administrative groups and lists users from all nested groups, giving a flattened view of privileged accounts.

### List a group's or a user's memberships

Resolve membership in one direction or the other.

```
automato member "Domain Admins"
```

Outcome: lists every user in the supplied domain group; the inverse query `automato user jdoe` returns all group memberships for a single user.

### Retrieve LAPS passwords

Pull the rotated local-administrator passwords for machines in the domain.

```
automato laps
```

Outcome: queries the domain for LAPS-managed systems and outputs each system's stored local administrator password (requires an account with LAPS read permission).

### Spray one password across a user list

Test a common password against many accounts in a single controlled pass.

```
automato spray users.txt 'Winter2026!'
```

Outcome: authenticates each username in the file against the domain with the given password and reports which accounts accepted it, writing hits to an output file for evidence.

### Check local admin rights on many hosts

Map where one credential gives local administrator access.

```
automato localadmin example.local jdoe 'Winter2026!' smb_hosts.txt
```

Outcome: for every IP in `smb_hosts.txt` (hosts with SMB open), attempts an SMB login as `example.local\jdoe` and reports on which machines that user is a local administrator.

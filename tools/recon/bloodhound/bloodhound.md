---
name: "bloodhound"
categories:
  - blackarch-recon
version: "v4.3.1.r61.ge8eda86"
homepage: "https://github.com/SpecterOps/BloodHound-Legacy"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-03-07"
build-system: node
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "SpecterOps/BloodHound-Legacy"
github-stars: 10625
github-pushed: "2026-03-02"
github-archived: false
github-issues: 93
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# bloodhound

> Six Degrees of Domain Admin — the legacy 4.x BloodHound GUI that maps Active Directory attack paths in a Neo4j graph.

## What it's for

BloodHound (legacy 4.x) ingests collector output — SharpHound on Windows or the [[bloodhound.py]] ingestor on Linux — into a Neo4j database and renders the identity graph: group memberships, logon sessions, ACL abuse edges, delegation settings and GPO links. Its core question is relational: given one compromised principal, what is the shortest abuse path to Domain Admins or other high-value targets, and which accounts (kerberoastable, AS-REP roastable, delegation-exposed) are worth attacking next. The 4.x line is upstream-deprecated and superseded by BloodHound Community Edition (CE, v5+, web-based, different data model); CE-format collections belong in CE, and 4.x-format collections belong here — the two are not interchangeable. CE collection from Linux is covered by [[bloodhound-ce-python]].

## Typical use cases

### Start the Neo4j database

One-time prerequisite for every session: the GUI only renders what sits in its graph database.

```
sudo systemctl start neo4j        # or: neo4j console (foreground)
```

Outcome: Neo4j listens on bolt://localhost:7687. On first run open http://localhost:7474, log in as `neo4j`/`neo4j` and set the new password the GUI will ask for.

### Launch the GUI and connect

```
bloodhound
```

Outcome: the Electron app opens a login dialog — accept the default database URL (bolt://localhost:7687), username `neo4j`, and the password set above. If the app cannot write its config, relaunch with `sudo bloodhound`. An empty graph appears once connected.

### Ingest a collector archive

```
# drag and drop the collector .zip (or individual *_users.json / *_computers.json / ... files) onto the GUI window
```

Outcome: the file menu shows upload/parse progress and the graph populates; node counts per label appear in the bottom status bar. Re-uploading a newer collection for the same domain merges by object identifier, so incremental re-collection after compromising a new account is the normal workflow.

### Collect from Linux with the Python ingestor

The quickest end-to-end loop when only credentials and network reach to LDAP are available (full flag coverage in [[bloodhound.py]]):

```
bloodhound-python -d example.com -u user -p 'Password1' -ns 198.51.100.10 -c All --zip
```

Outcome: one `<timestamp>_bloodhound.zip` written to the working directory; drop it onto the GUI as above.

### Mark compromised principals as owned

```
# search the principal in the search bar, right-click its node -> Mark as Owned
```

Outcome: the node is flagged (skull icon) and the database records `owned: true` for that object. Every "from Owned Principals" analysis query now starts at exactly the principals you control, so repeat this each time a new account falls.

### Shortest paths from owned principals to Domain Admins

Run in the raw query console (bottom bar):

```
MATCH p = shortestPath((n {owned:true})-[:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GPLink*1..]->(g:Group))
WHERE g.objectid =~ 'S-1-5-21.*-512' AND NOT n = g
RETURN p
```

Outcome: every shortest abuse chain from your owned nodes into each domain's Domain Admins group (RID `-512`), one component per domain in multi-domain data. The Analysis-tab equivalent is the prebuilt "Shortest Paths to Domain Admins from Owned Principals"; the edge list above is the GUI's default traversal set, trimmed to on-premises edges.

### List all kerberoastable accounts

```
MATCH (n:User) WHERE n.hasspn = true RETURN n
```

Outcome: users with servicePrincipalNames — their service tickets can be requested and cracked offline. Also available prebuilt as "List all Kerberoastable Accounts"; rank results by group membership ("Find Kerberoastable Members of High Value Groups") to crack the ones that matter.

### List AS-REP roastable users

```
MATCH (u:User {dontreqpreauth: true}) RETURN u
```

Outcome: accounts with Kerberos pre-authentication disabled, whose AS-REP hashes can be pulled without credentials and cracked offline. Prebuilt equivalent: "Find AS-REP Roastable Users (DontReqPreAuth)".

### Shortest paths to unconstrained delegation systems

```
MATCH (n)
MATCH p = shortestPath((n)-[:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GPLink*1..]->(m:Computer {unconstraineddelegation: true}))
WHERE NOT n = m
RETURN p
```

Outcome: which principals can reach hosts configured for unconstrained delegation — capturing that host's memory yields every authenticating user's TGT. Prebuilt equivalent: "Shortest Paths to Unconstrained Delegation Systems"; exclude domain controllers in the results view since they always have the flag.

### Find principals with write control over GPOs

```
MATCH p = (n)-[:GenericAll|GenericWrite|WriteDacl|WriteOwner|Owns]->(g:GPO) RETURN p
```

Outcome: principals able to edit Group Policy objects. Editing an abused GPO (immediate scheduled task, new local admin) hits every user/computer the GPO is linked to — follow `GPLink` edges from `g` to see the blast radius; the GPO's node info lists its GUID for editing the object on a controller.

### Review one principal's outbound object control

```
# search the account, click its node -> Node Info tab -> Outbound Object Control
# right-click any edge in the graph -> Help for the abuse and opsec notes on that edge
```

Outcome: the effective ACL edges this principal holds over others (GenericAll, GenericWrite, ForceChangePassword, WriteSPN, WriteDacl, ...), each with concrete abuse steps — the standard next move after landing any new account.

### Import a custom query pack

```
cp customqueries.json ~/.config/bloodhound/customqueries.json    # Linux
# Windows: %APPDATA%\bloodhound\customqueries.json
```

Outcome: on next start the GUI loads the pack and its entries appear in the analysis query list next to the prebuilts (shape: `{"queries": [{"name": ..., "category": ..., "queryList": [{"queryName": ..., "query": ...}]}]}`); queries composed inside the GUI are persisted to the same file.

## Bug bounty workflow with BloodHound

BloodHound is not a web-surface tool and the economics are different from everything else in this vault. It does not find a bug by touching a URL — it *maps* an Active Directory that you already have a credential in, and renders the shortest abuse chain from where you stand to Domain Admins. That single fact reshapes the entire workflow around one gate: **collection is a domain-wide, privileged, enterprise-visible action, and almost no public web bug-bounty scope authorizes it.** BloodHound belongs to engagements whose rules of engagement explicitly name internal Active Directory — internal/assumed-breach pentests, red-team engagements, and the handful of enterprise VDP/bug-bounty programs that list AD (and usually a provided test account or a dedicated lab forest) in scope. If the program scope is "*.example.com web assets," pointing a collector at a domain controller is out of scope the moment the first LDAP bind lands, regardless of what you find.

**The whole doc below assumes written authorization for domain enumeration and a named collection window.** Where a step is louder than the one before it, that is called out so you can stay at the quietest setting the engagement allows.

### Phase 0 — Authorization and blast-radius sizing (do this before any bind)

Before a single packet, settle four things in writing and keep them with your evidence:

- **Is AD collection in scope at all?** The answer is in the RoE, not inferred. "Internal network testing authorized" is not the same as "domain-wide directory enumeration and session harvesting on every workstation authorized." Get the second sentence explicitly.
- **DCOnly or full?** LDAP-only collection (`DCOnly`) reads the directory from a domain controller and never connects to member hosts. Full collection (`Default`/`All`) additionally hits every computer object for sessions, local-group membership, RDP/DCOM rights — thousands of SMB/RPC connections that light up EDR and NDR. Default to DCOnly and negotiate up.
- **Which domain/forest, and from which account.** Collection is scoped to the credential's domain unless you add forest-wide flags. A low-priv domain user is enough for LDAP and is the realistic "assumed breach" starting position — you do not need admin to collect.
- **Where the data lands.** The collection `.zip` is a complete directory map (every user, group, ACL, SPN, delegation setting). It is sensitive client data. Decide storage, encryption, and destruction up front; never upload it to a public Neo4j, a shared sandbox, or a third-party BloodHound instance.

### Phase 1 — Stand up the graph locally

Collection and analysis are separate machines-of-thought: you ingest once into a local Neo4j and query offline, so the directory is only touched during the collection window. Start Neo4j (`sudo systemctl start neo4j`), set the password, launch `bloodhound`, connect to `bolt://localhost:7687`. Nothing has touched the target yet — this is all local. Confirm you are on a **4.2/4.3-format** stack: `bloodhound-python` (the `pip install bloodhound` package, legacy branch) emits 4.x JSON and prints `BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)` at startup; `bloodhound-ce-python` and SharpHound 2.x emit CE-format data that this GUI cannot parse. Mixing the two is the single most common "the graph is empty after upload" failure.

### Phase 2 — Minimal-noise collection (DCOnly first)

The first collection should be the quietest one that still answers the escalation question. DCOnly reads the entire identity graph — groups, ACLs, SPNs, delegation, trusts, GPO links, container structure — from LDAP against one DC, with zero connections to member workstations:

```
bloodhound-python -d example.com -u svc_reader -p 'REDACTED' \
  -ns 198.51.100.10 -c DCOnly --zip -op example-dconly
```

That `-c DCOnly` expands (verified in the ingestor source) to `group, trusts, objectprops, acl, container` — none of which contacts a member host. You now have every ACL-abuse edge, every kerberoastable/AS-REP-roastable account, every delegation flag, and every trust, which is where the overwhelming majority of attack paths actually live. Only escalate to session-bearing collection if the RoE allows it and the DCOnly graph did not yield a path (Phase 4).

### Phase 3 — Mark reality into the graph

Import the `.zip`, then tell the graph what you actually control: search each compromised principal, right-click → **Mark as Owned** (sets `owned:true`). Mark high-value objects the program cares about if they are not already flagged (`highvalue:true`). This is what turns a generic directory map into *your* attack-path report: every "from Owned Principals" query now starts at exactly the accounts you hold, so the paths it draws are paths you can actually walk, not theoretical ones.

### Phase 4 — Shortest-path queries become the report narrative

Run the prebuilt "Shortest Paths to Domain Admins from Owned Principals" (Analysis tab) or the equivalent raw query. Each rendered path is a finding-in-waiting: a chain of concrete, individually-abusable edges from an account you proved you hold to Domain Admins. Walk each edge's right-click → **Help** (Abuse Info / Opsec / References tabs) to confirm the step is real and to capture the abuse technique for your write-up. A three-edge path — `svc_reader` has `GenericWrite` over a group, the group is `AdminTo` a server, a DA has a session on that server — *is* the report: impact (full domain compromise), preconditions (one low-priv account), and reproduction (three named edges), all on one screen.

### Phase 5 — Validate, bound, and write up

A path on the graph is a hypothesis built from directory metadata; the finding is the path you can defend. For each reported path: confirm the key edges are current (sessions in a DCOnly collection are absent by design — a session-dependent edge needs a session-bearing collection or live confirmation), note which edges you actually exercised versus which you are asserting from ACLs, and keep the exploitation itself inside the RoE (demonstrating you *can* kerberoast an account is usually fine; actually DCSyncing the domain usually is not without explicit sign-off). Export the path (Phase: *Reporting evidence*) and write impact-first.

## Where the money is with BloodHound

The "finding" here is an **attack path**, and its severity is the privilege at the end of the shortest one you can walk from an assumed-breach foothold. The table ranks the path classes BloodHound surfaces, the query/feature that reaches each, the collection noise it costs, and how likely the result is to be a duplicate or an already-known issue.

| Priority | Path / finding class | Query or feature | Collection cost | Typical severity | Dupe / noise risk |
|---|---|---|---|---|---|
| 1 | Owned → Domain Admins shortest path | "Shortest Paths to Domain Admins from Owned Principals" | DCOnly often enough | Critical | Low — it is *your* path from *your* foothold |
| 2 | DCSync rights (domain replication) | "Find Principals with DCSync Rights" (`DCSync\|GetChanges\|GetChangesAll`→Domain) | DCOnly | Critical | Medium — common misconfig, check it is reachable from you |
| 3 | ACL abuse edges (GenericAll/WriteDacl/ForceChangePassword/Owns) over privileged objects | Node Info → Outbound Object Control; raw edge queries | DCOnly | High–Critical | Low if chained to a concrete target |
| 4 | Unconstrained/constrained delegation to DA | "Shortest Paths to Unconstrained Delegation Systems"; `AllowedToDelegate` | DCOnly | Critical | Medium — DCs always flagged, exclude them |
| 5 | Kerberoastable high-value accounts | "Find Kerberoastable Members of High Value Groups" | DCOnly | High | Medium–High — kerberoasting is heavily reported |
| 6 | AS-REP roastable users | "Find AS-REP Roastable Users (DontReqPreAuth)" | DCOnly | Medium–High | High — trivially found by many tools |
| 7 | GPO write → mass code exec | principals with `GenericAll\|GenericWrite\|WriteDacl\|WriteOwner\|Owns`→GPO | DCOnly | High–Critical | Low — blast radius via GPLink is bespoke |
| 8 | Domain Users → High Value shortest paths | "Find All Paths from Domain Users to High Value Targets" | DCOnly | High | Medium — systemic, strong for a report narrative |
| 9 | LAPS read rights | "Find Computers where Domain Users can read LAPS passwords" | DCOnly | High | Low–Medium |
| 10 | Local-admin sprawl (Domain Users local admin) | "Find Computers where Domain Users are Local Admin" | needs LocalAdmin (host-touching) | Medium–High | Medium |
| 11 | DA logon exposure (creds on non-DC) | "Find Domain Admin Logons to non-Domain Controllers" | needs Session (host-touching) | High | Medium — depends on live sessions |
| 12 | Foreign-domain group membership / trust abuse | "Users with Foreign Domain Group Membership"; "Map Domain Trusts" | DCOnly | Medium–High | Low |

The pattern: everything in the top nine is reachable from a **DCOnly** collection — a low-privilege LDAP read, no member-host contact. The host-touching methods (rows 10–11) buy you session and local-admin reality at the cost of enterprise-wide SMB/RPC noise, so only reach for them when a path genuinely depends on a live session and the RoE permits it.

## Command recipes

All examples use `example.com` / the `198.51.100.0/24` (TEST-NET-2) DC. Flags below are verified against the `bloodhound-python` argument parser (legacy branch) and the ingestor's method-expansion logic.

### Collection methods at a glance (verified expansions)

The `-c` value decides both what you learn and how loud you are. These expansions are what the legacy ingestor actually does with each keyword:

| `-c` value | Expands to / collects | Touches member hosts? |
|---|---|---|
| `DCOnly` | `group, trusts, objectprops, acl, container` — the full identity/ACL/delegation graph from LDAP | **No** (LDAP to DC only) |
| `Default` | `group, localadmin, session, trusts` | **Yes** (localadmin, session) |
| `All` | `group, localadmin, session, trusts, objectprops, acl, dcom, rdp, psremote, container` (excludes `loggedon`, `experimental`) | **Yes** (localadmin, session, dcom, rdp, psremote) |
| `Group`, `Trusts`, `ObjectProps`, `ACL`, `Container` | individual LDAP-only reads | **No** |
| `LocalAdmin`, `Session`, `LoggedOn`, `RDP`, `DCOM`, `PSRemote`, `Experimental` | individual host-touching enumeration | **Yes** |

Rule of thumb: anything in the LDAP-only rows is DCOnly-safe; anything that enumerates sessions, local admins, or exec rights opens connections to every computer object (or every host in `--computerfile`). `LoggedOn` additionally requires local admin on each target to return data.

### Quietest useful collection — DCOnly, LDAP only

```
bloodhound-python -d example.com -u svc_reader -p 'REDACTED' \
  -ns 198.51.100.10 -c DCOnly --zip -op example-dconly
```

Expected: a single `example-dconly_<timestamp>_bloodhound.zip`, written after a handful of LDAP queries to one DC and **no connections to member hosts** (`DCOnly` → `group, trusts, objectprops, acl, container`; the `-c` help text literally reads "DCOnly (no computer connections)"). This is the collection to run first on every engagement.

### Collection over an existing SOCKS tunnel / specific DC

```
bloodhound-python -d example.com -u svc_reader -p 'REDACTED' \
  -dc dc01.example.com -gc dc01.example.com -ns 198.51.100.10 \
  --dns-tcp -c DCOnly --zip
```

Expected: `-dc`/`-gc` pin the LDAP and Global Catalog targets (avoids referral scatter when pivoting through one reachable DC); `--dns-tcp` forces DNS over TCP, which is what you want when UDP/53 is blocked on the pivot. `--dns-timeout N` (default 3s) helps on laggy tunnels.

### Authenticate without a cleartext password

```
# NT hash (pass-the-hash); format is LM:NT, use 32 zeros for an empty LM
bloodhound-python -d example.com -u svc_reader --hashes aad3b...:8846f7eaee8fb117ad06bdd830b7586c \
  -ns 198.51.100.10 -c DCOnly --zip

# Kerberos ccache (pass-the-ticket): point KRB5CCNAME at your ccache, then
bloodhound-python -d example.com -u svc_reader -k -ns 198.51.100.10 -c DCOnly --zip

# AES key
bloodhound-python -d example.com -u svc_reader -aesKey <hex> -ns 198.51.100.10 -c DCOnly --zip
```

Expected: `--hashes LM:NT`, `-k` (Kerberos ccache), and `-aesKey` are all accepted by the parser; `--auth-method {auto,ntlm,kerberos}` (default `auto`) tries Kerberos first and falls back to NTLM. `-no-pass` collects with a username only where the auth context is already established. These let you collect from a foothold credential you captured, without ever handling a cleartext password.

### Full collection — only when a path needs sessions, and the RoE allows it

```
bloodhound-python -d example.com -u svc_reader -p 'REDACTED' -ns 198.51.100.10 \
  -c All --exclude-dcs -w 4 --zip -op example-full
```

Expected: `-c All` expands to `group, localadmin, session, trusts, objectprops, acl, dcom, rdp, psremote, container` — note it adds `localadmin, session, dcom, rdp, psremote`, each of which **connects to every computer object** over SMB/RPC. This is loud. Two levers reduce the blast radius: `--exclude-dcs` keeps session/local-group enumeration off domain controllers (less likely to trip DC-focused detections), and `-w 4` drops the computer-enumeration worker count from the default 10 to slow the connection rate. There is no built-in throttle/jitter on the Python ingestor — if you need true rate shaping or stealth staging, that is SharpHound's `--stealth`/`--throttle`/`--jitter` territory on Windows (and a CE-format collection, so it goes into CE, not this 4.x GUI). Prefer never running `All` at all unless a specific session-dependent edge is the only path.

### Targeted re-collection with a computer list

```
bloodhound-python -d example.com -u svc_reader -p 'REDACTED' -ns 198.51.100.10 \
  -c Session --computerfile ./scope-hosts.txt --zip
```

Expected: `--computerfile` restricts host-touching methods to the lines in the file instead of every computer in the directory — the correct way to confirm a single session-dependent edge without re-sweeping the whole estate. Pair it with a short, named collection window.

### Raw Cypher the report actually leans on

Run these in the GUI's bottom Raw Query bar (or Neo4j Browser at `http://localhost:7474`). All use only edges/properties present in a DCOnly graph.

```
// Every shortest path from accounts you own into Domain Admins (-512), per domain
MATCH p = shortestPath((n {owned:true})-[*1..]->(g:Group))
WHERE g.objectid =~ '(?i)S-1-5-.*-512' AND NOT n = g
RETURN p
```

```
// Who can DCSync the domain (replication rights = full credential theft)
MATCH p = ()-[:DCSync|GetChanges|GetChangesAll|AllExtendedRights|GenericAll]->(:Domain {name:'EXAMPLE.COM'})
RETURN p
```

```
// All outbound object-control an owned principal holds (the "what's my next move" query)
MATCH p = (n {owned:true})-[:GenericAll|GenericWrite|WriteDacl|WriteOwner|Owns|ForceChangePassword|AllExtendedRights|AddMember|ReadLAPSPassword]->(m)
RETURN p
```

```
// Kerberoastable accounts that are also in a high-value group (crack the ones that matter)
MATCH p = shortestPath((n:User)-[:MemberOf*1..]->(g:Group))
WHERE g.highvalue = true AND n.hasspn = true
RETURN p
```

Expected: each returns a subgraph you can render, collapse, and export. `shortestPath()` returns one path per endpoint pair; swap to `allShortestPaths((a)-[*1..]->(b))` when you need every equally-short route (useful for showing a chokepoint). The `(?i)S-1-5-.*-512` suffix is the Domain Admins RID and is exactly the filter the built-in "Find all Domain Admins" query uses.

```
// Cross-domain / trust reach: principals in a foreign domain's groups
MATCH p = (n)-[:MemberOf]->(g:Group)
WHERE NOT n.domain = g.domain
RETURN p
```

```
// Trust map with direction (the "can I pivot to the parent/forest?" view)
MATCH p = (n:Domain)-[:TrustedBy]->(m:Domain)
RETURN p
```

Expected: the first mirrors the built-in "Users with Foreign Domain Group Membership" / "Groups with Foreign Domain Group Membership" and surfaces cross-domain footholds (a user in one domain who is privileged in another); the second renders trust direction, the precondition for any cross-domain escalation. Both are LDAP-only, so they come free with a DCOnly collection of each domain you are authorized to touch — and note that each foreign domain is a *separate* authorization question: a trust edge on the graph is not permission to collect the domain on the other end.

## Cypher query essentials

The Analysis-tab prebuilts cover the common cases, but every non-trivial path report eventually needs a hand-written query. BloodHound's query language is Neo4j Cypher; the GUI's Raw Query bar and Neo4j Browser (`:7474`) both run it. The model is small and worth owning.

**Node labels (4.x on-prem):** `User`, `Computer`, `Group`, `Domain`, `GPO`, `OU`, `Container`. Azure data adds `AZ*` labels (out of scope for an on-prem path report).

**Properties you filter on constantly** (verified from the built-in queries): `objectid` (the SID — RID suffixes `-512` Domain Admins, `-513` Domain Users, `-516` Domain Controllers, `-519` Enterprise Admins), `name`, `domain`, `enabled`, `highvalue`, `owned`, `hasspn` (kerberoastable), `dontreqpreauth` (AS-REP roastable), `unconstraineddelegation`, `admincount`, `operatingsystem`, `pwdlastset`.

**The edge vocabulary** that paths are built from (on-prem, present in a DCOnly graph): group/session — `MemberOf`, `HasSession`, `AdminTo`; ACL control — `GenericAll`, `GenericWrite`, `WriteDacl`, `WriteOwner`, `Owns`, `AllExtendedRights`, `ForceChangePassword`, `AddMember`, `AddSelf`, `WriteSPN`, `AddKeyCredentialLink`; credential theft — `DCSync`, `GetChanges`, `GetChangesAll`, `ReadLAPSPassword`, `ReadGMSAPassword`; delegation — `AllowedToDelegate`, `AllowedToAct`; exec — `CanRDP`, `ExecuteDCOM`, `CanPSRemote`, `SQLAdmin`; structure — `Contains`, `GPLink`, `TrustedBy`, `HasSIDHistory`.

### The five query shapes you reuse

```
// 1. Direct relationship — "who is directly X over Y"
MATCH p = (n)-[:GenericAll]->(g:Group {name:'DOMAIN ADMINS@EXAMPLE.COM'})
RETURN p

// 2. Transitive membership — the *-hop variable-length, the backbone of AD
MATCH p = (u:User)-[:MemberOf*1..]->(g:Group {name:'DOMAIN ADMINS@EXAMPLE.COM'})
RETURN p                       // every effective (nested) member, not just direct

// 3. Shortest path over any edge — the headline report query
MATCH p = shortestPath((n {owned:true})-[*1..]->(g:Group))
WHERE g.objectid =~ '(?i)S-1-5-.*-512' AND NOT n = g
RETURN p

// 4. Property hunt — "all accounts with attribute X"
MATCH (u:User) WHERE u.hasspn = true AND u.enabled = true RETURN u

// 5. Chokepoint — how many owned principals funnel through one node
MATCH p = allShortestPaths((n {owned:true})-[*1..]->(m {highvalue:true}))
RETURN p
```

### Patterns that matter in practice

- **`shortestPath()` vs `allShortestPaths()`.** The first returns one path per endpoint pair — clean for "can I reach DA." The second returns *every* equally-short path — use it to show a shared chokepoint (one group or host that many paths cross), which is the strongest remediation argument you can hand a defender.
- **Variable-length with an edge allowlist.** `-[:MemberOf|AdminTo|HasSession|GenericAll|WriteDacl|...*1..]->` restricts traversal to *abusable* edges, which is what you want for an attack-path claim. Bare `-[*1..]->` traverses everything including benign structure edges (`Contains`), so it is faster to write but can draw paths that aren't attacks. Match the edge list to the claim you're making.
- **Cap the hop count on large graphs.** `*1..` is unbounded and can hang Neo4j on a big forest. Bound it (`*1..6`) while exploring, and raise it only when a longer path is plausible.
- **Scope to your foothold.** Prefix with `{owned:true}` so the start set is exactly the principals you proved you hold — the resulting path is one you can walk, not a theoretical directory artifact.
- **Filter by SID, not display name.** `objectid =~ '(?i)S-1-5-.*-512'` is locale- and rename-proof; "Domain Admins" can be renamed, SIDs cannot. Every built-in high-value query uses the SID regex for this reason.
- **Count before you render.** On a dense graph, `RETURN count(p)` first — a query that returns 40,000 paths will freeze the renderer. Confirm the result size, then `RETURN p LIMIT 25` to draw a representative subset for the report.

Save a working query via the GUI so it persists to `customqueries.json` (see "Import a custom query pack" above); a reusable pack of your own path queries is the compounding payoff across engagements.

## Pitfalls, noise and scope discipline

This is the tool most likely in this vault to get a hunter removed from a program or a tester fired from an engagement, because its *normal* operation is a domain-wide privileged action. The failure modes:

- **Collecting against a domain the scope never authorized.** A web bug-bounty scope ("*.example.com, api.example.com") does not authorize AD enumeration. There is no "passive" BloodHound — even DCOnly performs authenticated LDAP reads of the entire directory. If AD is not named in the RoE with a credential or lab forest provided, do not bind. This is the number-one way to turn a finding into a scope-violation report against *you*.
- **Running `All`/`Default` by reflex.** `Default` includes `localadmin` and `session`; `All` adds `rdp, dcom, psremote` on top. Each opens SMB/RPC to **every computer object in the domain** — thousands of connections that reliably trigger EDR lateral-movement and recon detections, and in a monitored environment will get your foothold account locked mid-collection. Start DCOnly; escalate only for a specific session-dependent edge, scoped with `--computerfile`.
- **Hammering a production DC.** High worker counts and full collection against a single DC during business hours is both noisy and a stability risk on large forests. Lower `-w`, pin one DC with `-dc`, collect in an agreed window, and never collect in a loop unless asked.
- **Format mismatch (4.x vs CE).** `bloodhound-ce-python` and SharpHound 2.x produce CE-format JSON that this legacy 4.x GUI silently fails to ingest (empty graph, no error). Match the collector to the GUI: legacy `bloodhound-python` for this article's BloodHound; CE collectors for [[bloodhound-ce-python]]/CE. Confirm the startup banner.
- **Treating the graph as ground truth.** BloodHound draws edges from directory *metadata*. An `AdminTo`/`HasSession` edge from a weeks-old collection may be stale; a session in a DCOnly graph is simply absent. Reporting a path whose load-bearing edge you never confirmed is how a "Critical" gets downgraded to "informational" on triage. State which edges you exercised.
- **Over-exploiting the path you found.** Finding a DCSync path is a finding; actually replicating the domain's secrets, creating DA accounts, or touching real user data usually exceeds a bug-bounty RoE even when AD is in scope. Demonstrate reachability (the graph + a single low-impact confirmation), then stop and report.
- **Leaking the collection data.** The `.zip` and the Neo4j database are a full map of the client's identity infrastructure. Keep them local, encrypted, and destroyed per the engagement terms; never load client data into a shared or public BloodHound/Neo4j instance.
- **Credentials and noise in DNS.** `bloodhound-python` needs a resolvable DC; a wrong `-ns` produces a storm of failed lookups (noisy and slow). Set `-ns` to an in-scope resolver and use `--dns-tcp` where UDP/53 is filtered, rather than retrying blindly.

## Reporting evidence

BloodHound findings live or die on making an abstract graph legible to a triager who may not run Neo4j. Capture, per path:

- **The rendered path, as an image.** Lay out the shortest path from your owned principal to the target, collapse unrelated nodes, and export a PNG screenshot of exactly that chain. One clean path image communicates "low-priv account → Domain Admin in three hops" faster than any paragraph.
- **The underlying graph, as JSON.** Export the current graph (the GUI's Export Graph control writes the drawn graph to JSON; it is re-importable, so a triager can reproduce your exact view). Attach it alongside the image so the finding is verifiable, not just illustrative.
- **The Cypher you ran, verbatim.** Paste the raw query (e.g. the owned→`-512` shortestPath above) so the path is reproducible against a fresh collection. A reproducible query is the difference between "trust me" and a confirmable finding.
- **Per-edge abuse detail.** For each edge in the path, capture the right-click → **Help** → Abuse Info text (and References). This names the concrete technique for each hop (how `GenericWrite` over a group is abused, how a kerberoast is performed) and shows the triager you understand the chain, not just that a tool drew a line.
- **Node Info panels for the endpoints.** Screenshot the source principal's Node Info (proving it is a realistic low-priv foothold) and the target's (proving it is high-value), including the `owned`/`highvalue` flags and relevant properties (`hasspn`, `dontreqpreauth`, `unconstraineddelegation`).
- **Collection provenance.** Record the collector and version (`bloodhound-python` legacy, 4.2/4.3 format), the collection method (`DCOnly` vs session-bearing), the account used, and the collection timestamp — so the reader knows which edges are LDAP-derived facts and which depend on a live session that may have since changed.
- **Impact-first write-up.** Title the finding by its endpoint ("Low-privileged domain account `svc_reader` has a three-edge path to Domain Admins via GPO write and an admin session"), then preconditions (one low-priv account), then the path image + Cypher, then remediation keyed to the specific edges (remove the ACL, re-scope the GPO delegation, rotate the kerberoastable SPN account to a gMSA).

## References

- BloodHound Legacy (SpecterOps) repository — https://github.com/SpecterOps/BloodHound-Legacy
- BloodHound-Legacy prebuilt analysis queries (source of the built-in query names and Cypher) — https://github.com/SpecterOps/BloodHound-Legacy/blob/master/src/components/SearchContainer/Tabs/PrebuiltQueries.json
- BloodHound.py (dirkjanm) ingestor, legacy branch — flag set and collection-method expansion verified against `bloodhound/__init__.py` — https://github.com/dirkjanm/BloodHound.py
- bloodhound.py on PyPI (`pip install bloodhound` → `bloodhound-python`, legacy 4.2/4.3 format) — https://pypi.org/project/bloodhound/
- SpecterOps BloodHound documentation (CE; legacy notes) — https://bloodhound.specterops.io/resources/legacy
- SharpHound (CE, 2.x — produces CE-format data, not for this 4.x GUI) — https://github.com/SpecterOps/SharpHound

*Research supplement added 2026-11-16, grounded in the BloodHound-Legacy repository (PrebuiltQueries.json), the dirkjanm/BloodHound.py ingestor source, and SpecterOps' BloodHound documentation; flags and collection-method expansions verified against the ingestor argument parser (model: claude-opus-4-8).*

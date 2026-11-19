# Technology Vault

Technology-centric knowledge base: **what a technology is** (for humans)
and **how to hunt bugs on it** (for AI agents). Deliberately separate
from the tool-centric vault (`../tools`), which answers "what tool is
this and how do I run it". This vault answers "what is this technology
and how do I attack it".

## Structure

    tech/
      <technology>/            one folder per technology (lowercase-hyphen)
        <technology>.md        HUMAN page: what it is, what it's used for,
                               solid base of understanding (prose-first)
        <technology>-*.md      AGENT skill files: bug bounty methodology for
                               this technology — what tools to use, what to
                               look for, how to parse results

- A technology folder can carry several skill files when the surface
  warrants it (e.g. `-bug-bounty.md`, later `-hardening.md` or per-area
  files); start with one and split when it outgrows a single read.
- Overview pages are self-contained prose written for OFFENSIVE SECURITY
  PROFESSIONALS — a base of understanding aimed at target-modeling (what
  the technology is, where you'll meet it, how it behaves on the wire,
  what its characteristic mistakes look like), not developer onboarding.
  Skill files are
  written so an AI agent can execute the methodology: concrete
  commands, expected outputs, decision points, tool mapping.
- No tool notes here — reference tools by name; the tools vault owns
  their reference pages. Cross-vault links don't resolve in Obsidian,
  so name tools in prose/code, not wikilinks.
- Placeholders in examples: `example.com`, `198.51.100.0/24`,
  `ATTACKER_IP` — never real hosts, never private-lab addresses.
- Git-tracked; obsidian-git auto-commits (push off), same setup as the
  tools vault (theme and plugins inherited).

## Contents

Written (overview + methodology):

- [[fastapi|FastAPI]] — overview + [[fastapi-bug-bounty|bug bounty methodology]] (v0.4)
- [[graphql|GraphQL]] — overview + [[graphql-bug-bounty|bug bounty methodology]] (rewritten from HackTricks)
- [[jwt|JWT]] — overview + [[jwt-bug-bounty|bug bounty methodology]] (rewritten from HackTricks)
- [[oauth|OAuth 2.0 / OIDC]] — overview + [[oauth-bug-bounty|bug bounty methodology]] (rewritten from HackTricks)
- [[redis|Redis]] — overview + [[redis-bug-bounty|bug bounty methodology]] (rewritten from HackTricks)
- [[activemq|ActiveMQ]] — overview + [[activemq-bug-bounty|bug bounty methodology]] (upstream-grounded; evidence count corrected after port-marker noise check)
- [[ajp|AJP]] — overview + [[ajp-bug-bounty|bug bounty methodology]] (upstream-grounded; evidence count corrected after port-marker noise check)

Pass 1 wave 1 (engagement-grounded, upstream-verified; counts = strict command-context
gating, superseding the structure-pass marker estimates; ledgers in internal-coverage/):

- databases: [[mysql]] (84; area router + [[mysql-mariadb]] divergence page), [[mongodb]] (11), [[memcached]] (7)
- windows: [[iis]] (63), [[windows-secrets]] (16), [[rdp]] (4), [[sccm]] (1), [[wsus]] (2)
- network-access: [[ssh]] (281; area router + [[ssh-openssh]]), [[ftp]] (55; area router + [[ftp-vsftpd]]), [[telnet]] (2 service-side), [[finger]] (1)
- windows-services: [[winrm]] (87), [[rpc]] (72)

Pass 1 wave 2 (2026-11-12; counts = strict command-context gates; AD-core
counts rose sharply vs markers, several others collapsed — ledgers record
both; ipmi/upnp/irc kept first-class upstream-grounded per thin doctrine):

- web-platforms: [[flask]] (46), [[node-express]] (30), [[gitea]] (29), [[gitlab]] (7)
- directory-services: [[smb]] (95; router + [[smb-samba]] + [[smb-windows-native]]), [[ad-acl]] (47), [[kerberos]] (60)
- network-services: [[upnp]] (0 real chains — HTTPAPI banner noise; upstream-only), [[irc]] (1: UnrealIRCd), [[rsync]] (7), [[ntp]] (1 attack: Timeroasting)
- data/infra: [[postgres]] (14), [[cups]] (2: Antique, EvilCUPS), [[ipmi]] (1: Shibboleth), [[wordpress]] (14)

Structure pass 2026-11-12 (placeholders only, pending approval — evidence =
matched engagement records; each folder holds a `<unit>.md`
placeholder with its evidence line):

- directory-services: [[smb]] (32), [[ad-acl|AD ACL abuse]] (31), [[kerberos]] (24), [[ldap]] (18), [[ad-cs|AD CS]] (9), [[ntds|NTDS.dit / DC secrets]] (9), [[ntlm]] (6), [[gpo|GPO]] (2, thin)
- windows: [[mssql]] (14)
- web-platforms: [[wordpress]] (19), [[tomcat]] (15), [[git-exposed|Exposed .git]] (7), [[jenkins]] (6), [[joomla]] (4, thin), [[drupal]] (3, thin)
- network-services: [[nfs]] (12), [[dns]] (10), [[snmp]] (9), [[smtp]] (8), [[sip]] (5), [[mqtt]] (4, thin), [[pop3]] (3, thin)
- databases: [[postgres]] (25), [[elasticsearch]] (13), [[mongodb]] (10)
- containers: [[docker]] (13), [[kubernetes]] (6)

Structure pass 2 (evidence-driven discovery: nmap-service harvest + marker
probes; noise-checked — x11 dropped as User-Agent strings, minio/grafana
demoted to port-collision thin):

- web-platforms: [[flask]] (33), [[node-express|Node.js/Express]] (32), [[gitea|Gitea/Gogs]] (27), [[php-fpm|PHP-FPM]] (8), [[django]] (6), [[gitlab]] (6)
- network-services: [[upnp|UPnP/SSDP]] (41), [[irc]] (31), [[rsync]] (29), [[http-proxy|HTTP proxies]] (15), [[ntp]] (14), [[vnc]] (10), [[jetdirect]] (9), [[finger]] (6), [[mdns]] (6), [[telnet]] (5)
- hardware: [[ipmi|IPMI/BMC]] (24)
- databases: [[memcached]] (7)
- messaging: [[kafka]] (5), [[rabbitmq|RabbitMQ/AMQP]] (6)

Structure pass 3 (user-approved thin units, 2-4 files each):

- network-services: [[svn]] (4), [[distcc]] (4), [[java-rmi|Java RMI]] (4), [[pptp]] (3), [[tftp]] (2)
- databases: [[etcd]] (4), [[cassandra]] (4), [[couchdb]] (3), [[minio]] (2), [[oracle-db|Oracle DB/TNS]] (1)
- messaging: [[activemq]] (3)
- web-platforms: [[grafana]] (4), [[kibana]] (3)

Thin units are first-class, not filler: least-documented surfaces are the
hardest and the most valuable to cover — their enrichment leans on upstream
documentation, advisory history, and default-configuration analysis, at the
same depth as evidence-rich units (user direction, 2026-11-12). Zero-evidence, skipped: consul,
zookeeper, sharepoint, coldfusion, struts, uac-bypass.

Pass 1 wave 3 (2026-11-12; area splits resolved by evidence — several parent
expectations reversed; squid/ split out of http-proxy/):

- directory-services: [[ldap]] (48; router + [[ldap-ad]] — OpenLDAP gate failed: "$OpenLDAP:" banner pollution), [[ntds]] (40), [[ad-cs]] (29), [[ntlm]] (38), [[gpo]] (6)
- web-platforms: [[tomcat]] (14), [[http-proxy]] (router) + [[squid]] (5), [[git-exposed]] (18), [[jenkins]] (3, thin), [[joomla]] (3), [[drupal]] (2)
- network-services: [[dns]] (router; AD-integrated 14 > BIND 3 — reversed vs expectation) + [[dns-bind]] (thin), [[nfs]] (13), [[snmp]] (12), [[smtp]] (router + [[smtp-exchange]] thin 4 + [[smtp-postfix]]; Exim gate failed), [[vnc]] (1: Cascade — hint was base64 noise), [[mdns]] (5)
- data/services: [[mssql]] (23), [[elasticsearch]] (3, thin), [[jetdirect]] (3, thin), [[sip]] (2, thin), [[kafka]] (2, thin)

Pass 1 wave 4 (2026-11-12 — the thin tail, all first-class):

- web/dev: [[django]] (9), [[php-fpm]] (12), [[svn]] (1), [[java-rmi]] (4)
- data-stores: [[etcd]] (2), [[cassandra]] (0 — unobserved, upstream-only), [[couchdb]] (0 — unobserved, upstream-only), [[minio]] (2), [[oracle-db]] (0 — upstream-only)
- network: [[mqtt]] (2), [[pop3]] (2), [[distcc]] (0 — unobserved, upstream-only), [[tftp]] (2)
- misc: [[pptp]] (3), [[grafana]] (4), [[kibana]] (3), [[rabbitmq]] (6)

Pass 1 closure (wave 5, in flight): docker + kubernetes (missed in wave
planning — audit caught it). Zero-evidence units carry stub ledgers so the
pass-2 engagement-by-engagement diff has complete coverage data.

Pass 1 wave 5 (closure, 2026-11-12): [[docker]] (30 — structure-pass hint
undercounted daemon-adjacent patterns), [[kubernetes]] (5).

**PASS 1 COMPLETE: 76/76 units enriched** (5 imported/rewritten + 71
evidence-mined/upstream-grounded). Every unit: overview page (offensive
audience) + agent methodology; multi-implementation areas carry router +
implementation pages (mysql/mariadb, ssh/openssh, ftp/vsftpd, smb/samba +
windows-native, ldap/ad, dns/bind, smtp/exchange + postfix,
http-proxy/squid). Evidence ledgers in internal-coverage/ carry the honest
command-context counts (many structure-pass estimates collapsed; several
rose). Pass 2 (engagement-by-engagement coverage audit against the ledgers) is
next.

Pass 2 extensions + new units (2026-11-12, owner-approved; gates honest — see internal-coverage/pass2-report.md):

Extensions: ssh (weak keys CVE-2008-0166, CA cert forgery), dns (unbound-control),
node-express (ORM injection), django (FileBasedCache pickle), snmp (trap injection),
git-exposed (write-side git abuse), php-fpm (Xdebug), docker (host-PID ns loot, lxd),
mysql-mariadb (wsrep CVE-2021-27928), smtp (maildrop LFI, OpenSMTPD CVE-2020-7247),
ftp (FXP pivot), [[haproxy]] (new impl page under http-proxy).

New units — wave A: [[sqlite]] (43), [[laravel]] (14), [[keepass]] (13),
[[monitoring]] area ([[monitoring-zabbix|zabbix]], [[monitoring-cacti|cacti]]; nagios/pandora/nsclient/centreon sections),
[[file-processing]] area ([[file-processing-exiftool|exiftool]], [[file-processing-imagemagick|imagemagick]]; ffmpeg/pdfkit sections), [[ansible]] (5).
New units — wave B: [[ruby-on-rails]] (6), [[websocket]] (7), [[spring-boot]] (3),
[[teamcity]] (3), [[mattermost]] (3), [[roundcube]] (4), [[openvpn]] (4).
Rejected by gate: jackson-databind (1 real chain; marker noise) — pending owner
ruling on java-deserialization class unit.

New units — wave C: [[web-server]] area ([[web-server-nginx|nginx]] 19, [[web-server-apache|apache]] 10; caddy/other sections; 50 machines total — largest unit), [[log4j]] (2, thin), [[x11]] (2, thin — original structure-pass kill was correct), [[code-runner]] (5), [[ml-models]] (4), [[aws]] (6), [[wifi]] (4).
New units — wave D: [[latex]] (3), [[fail2ban]] (3), [[gophish]] (3), [[package-managers]] (7), [[gpg]] (9), [[next-js]] (3).
New unit — final: [[java-deserialization]] (9; owner-approved class framing; .NET ViewState chains excluded by adjudication).

**PASS 2 COMPLETE (2026-11-12).** 260 writeups audited against the pass-1
ledger; 12 extensions applied to existing units; 26 new units/areas created
(owner-approved set, SSRF rejected, jackson-databind gate-failed);
haproxy implementation page added under http-proxy. Honest gate counts in
internal-coverage/ ledgers; every CVE verified against primary sources during writing.

HackTricks enrichment wave (2026-11-13): 29 units enriched additively from the upstream reference (155 techniques added, 216 skipped as already-documented), rewritten in vault voice with CC BY-NC 4.0 credit; includes new [[ldap-injection]] technique page, LPD/IPP phases under [[jetdirect]], OMI section under [[winrm]], request-smuggling sections under [[http-proxy]]. Notable upstream errors caught at the gate: phantom Erlang/OTP TFTP advisory, nonexistent metasploit/nmap module names, JDK LDAP-codebase date error, a phantom 2026 CVE trap note. 4 falsified pre-existing claims surgically corrected (MS18-055 bulletin name, @RSYNCD banner semantics, OMI operation name, TigerVNC flag).

Bucket-2 wave 1 (owner-approved comprehensive expansion, upstream-only units): [[ics]] area (router + 6 implementation pages + safety-doctrine runbook — passive-first ICS method), [[h2-database]] (H2/hsqldb console RCE chains), [[jdwp]], [[epmd]], [[sap]] (incl. saprouter pivot), [[ipsec-ike]] (IKE PSK offline dictionary), [[turn]], [[socks]], [[r-services]] (rexec/rlogin/rusersd family + router), [[ws-discovery]], [[nats]], [[ibmmq]], [[tidb]], [[redshift]], [[influxdb]]. All rewritten from HackTricks with CC BY-NC credit; CVEs re-gated against primary advisories (5 HT miscitations corrected on OPC UA alone; CVE-2018-5389 hint corrected to DH-reuse dictionary attack; CVE-2019-20933 range fixed to <1.7.6).

Bucket-2 wave 2 (final, 2026-11-13): [[splunk]], [[veeam]], [[solarwinds]], [[hadoop]], [[tiller]], [[rtsp]] (PPPP/CS2 P2P camera section), [[adbd]], [[insight-manager]], [[gdbserver]], [[iso-8583]], [[iscsi]], [[glusterfs]], [[check-point]], [[sd-wan]], [[small-services]] (whois/ident/echo trio). Bucket-2 expansion complete: 30 upstream-only units total, every CVE primary-source-verified, unverifiable dispatch hints refused rather than written (sd-wan cert leak, gdbserver/adbd carry zero CVEs by design).

pentest-book ingest wave 1 (2026-11-13, six2dez/pentest-book — unlicensed source, facts-only rewrites with attribution): 17 service units enriched from the ports cheat sheet (42 additions; ftp/dns/tftp/finger/pop3/nfs/ipsec-ike all-dupes — prior waves already cover them); 10 cloud/auth/AD units enriched (aws 12, kubernetes 16 incl. corrected admission-bypass material, oauth, graphql, AD core); new units [[azure]], [[gcp]], [[llm]] (prompt-injection surface, OWASP GenAI-verified), [[mobile]] area (android + ios), [[macos]], [[edr]]. Source errors caught at the gate: swapped Kerberoast/ASREPRoast tool labels, nonexistent impacket scripts, kubectl-patch TOCTOU claim falsified at API level, dcsync permission-count error, CVE-2023-32364 mislabeled TCC (actually sandbox escape).

pentest-book wave 2 (2026-11-13): new units [[fortinet]] (SSL VPN/FortiOS, PSIRT-verified; dispatch hint about a 2019 backdoor cohort refuted via CNA records), [[ssl-tls]], [[browser-extension]], [[hardware]] (area router: UART/JTAG/SPI, firmware extraction, RFID/sub-GHz — vault's first bench-methodology unit), domain-fronting section into [[http-proxy]]. Dispatch-hint error rate this campaign: 3 (FortiOS cohort, FG-IR mapping, k8s TOCTOU) — all caught by children against primary sources.

Azure supplement wave (2026-11-14, MicroBurst source-analysis enrichment): [[azure]] both pages supplemented additively (owner-approved comprehensive scope) from NetSPI/MicroBurst repo analysis — 7 gaps closed: Global Administrator elevateAccess bridge (verified against Microsoft Learn elevate-access doc + Sentinel detection template), token-only bearer-token attack state (new Phase 4B with verified ARM REST shapes from MicroBurst source), automation RunAs pivot identities (new Phase 3A), control-plane identity-token family (Arc/ACR/deploymentScripts), guest-side VM extension protectedSettings decryption incl. Wire Server 168.63.129.16 (new Phase 4C), Kudu command execution + Entra token decryption on App Services (new Phase 8B), and Phase 6 persistence additions (Bastion shareable links, DSC ApplyAndAutoCorrect, owner-mint webhook). Appendix A now maps MicroBurst functions to phases. Additions-only contract kept; version 1.0 → 1.1; both provenance lines extended. No corpus ledger rows (upstream by design).

Azure supplement 2 (2026-11-14, PowerZure source-analysis): [[azure-bug-bounty]] Phase 5 additions — VM OS-disk SAS export appended to the Contributor bullet, new bullets for Custom Script Extension/UserData-channel execution (beyond Run Command) and Intune script push as fleet execution; Appendix A PowerZure clause expanded from a bare mention to a full function-to-phase mapping. Provenance extended. No overview changes.

CloudPentestCheatsheets ingest (2026-11-13, dafthack, MIT): azure +10, aws +13, gcp +8 verified additions; 2020-era stale material excluded by name (MSOnline retired cmdlets, WeirdAAL dormant, legacy gsutil ACL chains); AzureHound flows re-verified against current SpecterOps docs (source link dead).

HowToHunt ingest (2026-11-13, KathanP19, GPL-3.0, technology-shaped subset only — vuln-class dirs excluded per shape rule): dns +6 (dangling-CNAME takeover verification, new coverage), wordpress +4 (CVE-2018-6389 gate corrected to NVD's through-4.9.2), graphql/jwt/drupal/http-proxy +1 each; oauth all-dupes. jira source dir link-only; aem flagged as unit-proposal candidate (owner-gated).

New unit — [[aem]] (Adobe Experience Manager, upstream-only from HowToHunt CMS source + Adobe PSIRT-verified history; dispatch CVE hints refuted via CVE Program API — trap note in Phase 5).

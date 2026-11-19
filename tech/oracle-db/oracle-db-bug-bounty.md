---
technology: "Oracle DB / TNS"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus trace = 1 writeup (nmap oracle-tns probe name only; no attack chain) — upstream-verified (Oracle Security Alert CVE-2012-1675, Oracle 19c docs: system-privilege accounts table + password guidelines, ODAT wiki + README (quentinhardy/odat), nmap NSE docs for oracle-sid-brute, Kali tnscmd10g page, metasploit module library sid_brute/oracle_login) + supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/1521-1522-1529-pentesting-oracle-listener.md), CC BY-NC 4.0, accessed 2026-11-13 (oracle-tns-version NSE verified on nmap nsedoc, ODAT Kali packaging verified on kali.org, UTL_FILE/PREPROCESSOR gating verified against Oracle 21c docs) + Supplement 2 (2026-11-13): six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13 (oscanner usage verified on kali.org; Oracle9i 9.2 default-accounts chapter verified on docs.oracle.com)"
provenance: "hybrid: corpus-grounded (2 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# Oracle DB / TNS bug-bounty methodology

Companion overview: [[oracle-db]].

Scope note: this unit has no real observed chain — one engagement's nmap output names the `oracle-tns` probe and nothing more. Everything below is upstream-derived (Oracle advisories/docs, tool docs) and labeled as such: it is a method for a surface the observed record never actually attacks, which makes the verified-tool basics the core value.

## Phase 0 — Recon: the listener

```
1521/tcp open  oracle-tns
nmap -sV -p 1521-1560,1526 198.51.100.x
nmap --script=oracle-sid-brute -p 1521-1560 198.51.100.x
```

Speak raw TNS without any client login (tnscmd10g, packaged in Kali, from red-database-security):

```bash
tnscmd10g status  -h 198.51.100.x -p 1521    # listener status: version, SIDs/services already registered
tnscmd10g version -h 198.51.100.x            # TNS version banner
tnscmd10g --rawcmd '(CONNECT_DATA=(COMMAND=services))' -h 198.51.100.x   # arbitrary CONNECT_DATA via --rawcmd
```

Decision points: `status` output usually contains the listener version (patch-level recon) and often the SID outright — skip Phase 1 when it does. Also scan for the satellites: the 1521-1560 listener range, 8080 (XDB HTTP), 5500 (OEM Express).

## Phase 1 — SID discovery

Login requires a SID/service name. Three verified tools, one wordlist space (`orcl`, `prod`, `XE`, `PLSExtProc`, `SIM` — Kornbrust's list ships with nmap):

```bash
nmap --script=oracle-sid-brute --script-args=oraclesids=/path/sidfile -p 1521 198.51.100.x
./odat.py sidguesser -s 198.51.100.x -p 1521
./odat.py servicenameguesser -s 198.51.100.x -p 1521     # ODAT ≥5.0: service names, not just SIDs
```

Metasploit's `auxiliary/scanner/oracle/sid_brute` is the framework-side equivalent.

## Phase 2 — Credentials: defaults, then brute

Default-cred context (era-aware, from Oracle's own docs):

- **Legacy era (9i/10g, some 11g upgrades)**: the classic pairs — `scott/tiger` (demo schema, the textbook), `system/manager`, `sys/change_on_install`, `dbsnmp/dbsnmp`, `outln/outln`, plus schema accounts (`mdsys`, `ctxsys`, …) with name=password forms. Try these first; on unhardened legacy they land.
- **Modern (11g+)**: installs ship accounts **locked with expired passwords** (DBCA creates `SYS`, `SYSTEM`, `DBSNMP`); the residual risk Oracle itself documents is *upgraded* accounts still on defaults — on any session, `SELECT * FROM DBA_USERS_WITH_DEFPWD;` enumerates them.
- **Applications**: E-Business Suite/APEX deployments add app-level accounts (`apex_public_user`, DAD credentials) often as strong as the worst config file.

Brute-force with the SID from Phase 1:

```bash
./odat.py passwordguesser -s 198.51.100.x -p 1521 -d ORCL --accounts-file accounts.txt
msfconsole → auxiliary/scanner/oracle/oracle_login       # Oracle RDBMS Login Utility
```

Login check without guessing: `./odat.py all -s 198.51.100.x -p 1521 -d ORCL` runs the whole module battery (ODAT's docs say run `all` first on any Oracle you meet).

## Phase 3 — Inside: PL/SQL gates to OS execution

With any valid session, ODAT's module list is the execution catalog (each has `--test-module` to check privileges first; `--sysdba` upgrades where the account has DBA rights):

```bash
./odat.py utlfile -s 198.51.100.x -p 1521 -d ORCL -U scott -P tiger \
        --putFile /tmp shell.sh shell.sh          # UTL_FILE write
./odat.py dbmsscheduler -s ... --exec "..."       # DBMS_SCHEDULER job execution
./odat.py java -s ... --javaCodeFile exec.java   # Java stored procedures → Runtime.exec
./odat.py externaltable -s ...                   # external tables → file read
./odat.py privesc -s ...                         # known DB-side privilege-escalation paths
```

Common error `ORA-01031: insufficient privileges` (ODAT docs) = the account lacks the rights for that module; pivot to `privesc` or another package. SQL*Plus remains the interactive fallback: `sqlplus scott/tiger@198.51.100.x:1521/ORCL`.

## Phase 4 — CVE-2012-1675: TNS Listener Poison (verified upstream)

Oracle Security Alert CVE-2012-1675 (April 2012, disclosed by Joxean Koret): remotely exploitable **without authentication**, CVSS 7.5. The attacker remotely registers a database instance with the listener; the listener then routes future client connections for that instance to the attacker's host — harvesting credentials from every connecting client. Affected (per Oracle): 10.2.0.3–10.2.0.5, 11.1.0.7, 11.2.0.2–11.2.0.4 (11.2.0.4 added in rev 3).

```bash
./odat.py tnspoison -s 198.51.100.x -p 1521 -d ORCL --test-module    # check listener vulnerability
```

Mitigation upstream is secure-register/IPC config — an unpatched listener with remote registration open is reportable on its own.

## Phase 5 — Side doors and adjacent surfaces

- **XDB (XML DB HTTP) on :8080** — `ANONYMOUS` account unlocks HTTP access; the `xdb` schema's folders and old XDB bugs are a separate web-tier surface.
- **PL/SQL gateway / APEX** — mod_plsql / ORDS DADs mapping URLs to procedures; `APEX_PUBLIC_USER` is the canonical gate account; URL-to-procedure mapping mistakes (executable procedure reachable by name) are the classic web finding.
- **OEM Express :5500** — DBA console; creds = game over, same as SQL*Plus sysdba.
- **extproc / external procedures** — listener-configured external procedure agents have their own execution history; check `listener.ora` configs on any file-read primitive.

## Phase 6 — Reporting

Rank: unauth listener poisoning > OS command execution via PL/SQL packages > default-cred login > SID/credential harvest position > version disclosure. Recommend: listener patch level + secure registration (CVE-2012-1675 guidance), locked/expired default accounts verified against `DBA_USERS_WITH_DEFPWD`, least-privilege grants on UTL_FILE/DBMS_SCHEDULER/Java, gateway DADs audited.

## Tool map (prose)

odat (Oracle Database Attacking Tool — the all-in-one: sidguesser, servicenameguesser, passwordguesser, utlfile, dbmsscheduler, java, externaltable, tnspoison, privesc; tested against 10g–19c) as the primary weapon; tnscmd10g for raw listener commands; nmap NSE oracle-sid-brute (+ the `oracle-sids` wordlist); metasploit's `auxiliary/scanner/oracle/sid_brute` and `auxiliary/scanner/oracle/oracle_login`; SQL*Plus (or Oracle's free instant client) for interactive sessions. The tools vault holds per-tool reference notes.

## Supplement (2026-11-13): listener/TNS refinements from the HackTricks cross-check

Rewritten from HackTricks (src/network-services-pentesting/1521-1522-1529-pentesting-oracle-listener.md), CC BY-NC 4.0, accessed 2026-11-13 (the referenced `pentesting-oracle-database.md` page does not exist in the local HackTricks tree — only the listener page was available). Tool and gating claims re-verified against nmap nsedoc, kali.org, and the Oracle 21c documentation before writing.

- **A second nmap listener probe**: beyond `oracle-sid-brute`, the `oracle-tns-version` NSE decodes the **VSNNUM** version number out of the TNS listener — a version read that does not need any client library (runs under plain `-sV`). Use it for the Phase 0 patch-level picture before tnscmd10g/ODAT interaction.
- **ODAT installation reality**: on Kali it is packaged — `sudo apt install odat` — and the project's GitHub Releases page ships bundled builds that carry the Oracle client dependencies, which is otherwise the painful part of standing ODAT up.
- **Report the gate, not the module name (UTL_FILE / external tables)**: Oracle's own 21c documentation frames UTL_FILE access as the *intersection* of two privilege layers — READ/WRITE grants on the DIRECTORY object **and** the database process's OS permissions on the backing directory (files it creates are owned by the shadow-process owner, i.e. the oracle OS account). The external-table `PREPROCESSOR` clause is the same story with execution: it runs your named program for every data file, gated by access to its directory object, executing as the database's OS user. When a chain stalls at `utlfile`/`externaltable`, check both layers before concluding the account is useless — and when it succeeds, the finding is "OS file write/read as the oracle user", which is still not OS command execution until a consuming path exists. Symmetrically, never write up "SYSDBA = server compromise": listener access, a login, or SYSDBA administrative privilege alone does not establish an OS-level outcome.
- **Listener-administrative credentials are not database credentials**: on legacy listeners, the administrative operations (the lsnrctl CONTROL block — stop/status/config saves) are protected separately, by a listener password, from database logins. A leaked database account does not unlock listener administration and vice versa; when a target exposes both surfaces, test the credentials against the right one before declaring them dead.

(HackTricks' remaining listener content — the 1521 default, the version → services/SIDs → credentials workflow, ODAT `all`, and the authorization caution on brute force — was already covered in Phases 0-2 above.)

## Supplement 2 (2026-11-13): ports cheat-sheet cross-check

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); oscanner verified against its Kali package page (usage syntax included), the Oracle 9i default-accounts document against docs.oracle.com itself. The tnscmd10g listener verbs, the nmap oracle scripts, the ODAT module battery (sidguesser / passwordguesser / all / utlfile / externaltable) and the metasploit sid_brute / oracle_login pair were already covered in Phases 0–3 and Supplement 1 — skipped.

- **oscanner** — the Oracle assessment scanner that predates ODAT's all-in-one era: `oscanner -s 198.51.100.x -P 1521` enumerates SIDs off the listener and attempts default-credential validation per SID in one run (Kali packages it). ODAT remains the primary; oscanner is the lightweight second opinion on stubborn listeners, and its SID service file doubles as another wordlist source for Phase 1.
- **Oracle XML DB's FTP listener on 2100/tcp** — XDB can expose the database filesystem through an FTP front end on 2100, the file-transfer sibling of its HTTP front end on 8080 (Phase 5). The accounts that gate it are the database's own, and Oracle's 9.2-era documentation carries the full default-accounts table — with the explicit note that DBCA locks and expires every default account on a successful install. So an *answering* default login on 2100 means one of two failures: an account unlocked by hand, or an upgraded database that never rotated — both reportable. Same credential move as Phase 2, one port over.

---
technology: "FTP"
kind: overview
audience: offensive-security
family: network-services
layer: file-transfer protocol (TCP/21 control + dynamic data)
standards: "RFC 959 (FTP), RFC 4217 (FTPS)"
provenance: "corpus-grounded (56 writeups with ftp command-context evidence; 24 carry product markers — vsftpd dominant) + upstream-verified (RFC 959 semantics, RFC 2428 EPRT address-family semantics, vsftpd/Red Hat docs, nmap ftp-bounce NSE docs). Enrichment pass 1, 2026-11-12. Pass 2 extension 2026-11-12: FXP server-to-server/bounce section added (Pure-FTPd FXP+IPv6 banner advertisement, EPRT |2| third-party data connects as scan oracle and pivot to IPv6-only-bound services)."
---

# FTP

> The clearance-rack file server: plaintext credentials on the wire, anonymous logins left open, and a control protocol whose PORT command was designed to make the server connect anywhere you point it.

## What it is

FTP (File Transfer Protocol, RFC 959) predates security entirely: the control channel on **21/tcp** carries commands and credentials in the clear, and every data transfer opens a second connection — classic active mode (server connects back, per PORT) or passive mode (client connects out, per PASV; FTPS wraps both in TLS per RFC 4217). The implementation spread in the observed record is a monoculture with a tail: **vsftpd** dominates (the overwhelming majority of product-marked engagements — it is the default on most Linux distros and has its own page: [[ftp-vsftpd|vsftpd attack surface]]), followed by thin slices of **Pure-FTPd** (Debian-family default, two observed engagements), **ProFTPD** (two observed mentions — one /etc/passwd listing and one config context only), **FileZilla Server** on Windows (one observed engagement, FTPS with a self-signed cert), and **Microsoft IIS FTP** (two observed engagements — anonymous shares on Windows hosts).

The operator takeaway: FTP boxes are file oracles. Anonymous read gets you the backup/config/credentials file that owns the rest of the engagement; the plaintext control channel is a capture prize; and the auth layer reuses system credentials more often than not.

## The anatomy that matters on an engagement

    client → 21/tcp control channel (USER / PASS in cleartext, banners announce product+version)
         → active mode: server connects back per PORT (bounce-attack heritage)
           passive mode: server opens a high port per PASV (normal today)
    auth: anonymous / ftp (no password) · local system users via PAM (password reuse!)
    storage: /var/ftp or /srv/ftp (anon root, often a chroot) · user homes (local logins)

Three structural properties drive most findings:

- **Banner + auth prompt before any handshake crypto** — `220 (vsFTPd 3.0.5)` or `220 Microsoft FTP Service` leaks product and version on first connect; the `331` reply after USER confirms the account-name oracle.
- **Anonymous login is a configuration decision, not a feature** — `anonymous`/`ftp` with any password (convention `anonymous@`) maps to a filesystem root somebody pointed at data. Four observed engagements got their first file from an anonymous share.
- **Credentials are PAM-backed system passwords** — when local users can log in (`local_enable`), every FTP password is an SSH/sudo password somewhere (field-observed: web-app creds replayed successfully against FTP).

## Protocol-common attacks (any implementation)

Frequencies across the observed record follow each pattern.

- **Anonymous login + mirror the share** (the dominant observed pattern, plus banner-only mentions): `ftp 198.51.100.10` → `anonymous`/`anonymous@`, or non-interactive `wget -m ftp://anonymous:anonymous@198.51.100.10` (one observed chain; add `--no-passive-ftp` when the data channel must be active — field-observed). Windows IIS shares respond to `dir` with the true layout (`\Backups\`, `\Engineer\` — one observed chain leaked `backup.mdb` and a password-holding zip this way). Always `type binary` before `get` on anything that is not text.
- **Credential brute / password reuse** — hydra's ftp module works, but the observed pattern that actually pays is **reuse**: passwords harvested from web apps, configs or databases replayed against FTP user auth (field-observed). FTP banners make cheap username oracles: `331 Please specify the password` (valid user) vs `530 This FTP server is anonymous only` / `530 Login incorrect` timing differences per product.
- **FTP bounce (PORT abuse)** — RFC 959 lets the client tell the server where to send data; a server that accepts a PORT pointing at a *third* host becomes your scanner/spammer. Modern servers sanity-check PORT by default, but the check is a configuration setting — the FXP bullet below is the configuration that deliberately re-enables third-party data connections. Cheap to check and a real finding when it fires: nmap's `ftp-bounce` NSE script probes it directly (`--script ftp-bounce`), and idle/FTP-bounce scanning rides the same behavior. Report only with the confirmed third-party connect.
- **FXP server-to-server transfer (the bounce that ships as a feature)** — FXP moves data between two *servers* while the client only orchestrates over the control channel: your PORT/EPRT names a third host instead of yourself, and the server opens the data connection there. FXP-enabled servers (Pure-FTPd announces it in the banner, often beside IPv6/RFC 2428 support) have intentionally turned the third-party check off — the bounce primitive as a supported feature. Two ways it pays: as a **connect/port-scan oracle**, `EPRT |2|<address>|<port>|` followed by `LIST` reflects connection success or failure in the listing response; and because EPRT's `|2|` selects the IPv6 address family, the pivot reaches interfaces that never appeared on the IPv4 scan (field-observed: a service bound only on the IPv6 face, invisible to port scans of the IPv4 address, was reached through the FTP data channel and carried the next attack surface — an rsync daemon answering on a port the external scan had no idea existed). Workflow: read the banner for FXP/RFC 2428 support, learn the host's IPv6 address (EPRT yourself a listener and LIST to confirm the outbound connect), then bounce-scan both families through it. Reach beyond scan value: any internal-only, one-arm service on a segment the FTP host can touch is now yours to talk to.
- **Plaintext capture** — control-channel USER/PASS and data both cross the wire unencrypted unless FTPS; on a shared segment or after a traffic-interception foothold, FTP creds are a gift. (FTPS's self-signed-cert warning dialog is also a social-engineering surface — field-observed.)
- **File-oracle hunting inside the share** — after login (anonymous or credited), the observed pattern is triage by name: `.bak`, `backup*`, `*.mdb`, `*.zip`, `.git`, dotfiles (`ls -la` equivalent: `ls -a` or `ftp> ls -la` where supported), config and encryption artifacts (one observed share: `.drupal.txt.enc` — an OpenSSL `Salted__` file that cracked to CMS admin). FTP listings beat HTTP directory listings because nobody indexes them.

## Where you'll meet it

- **Linux estates** — vsftpd on 21, usually stock config; the page [[ftp-vsftpd|vsftpd attack surface]] covers the 2.3.4 backdoor history and the userlist/PAM behaviors.
- **Windows file drops** — IIS FTP with anonymous enabled, sitting next to the web app it feeds; Windows boxes where the share holds the app's own backups.
- **Appliances and legacy** — FTP services on printers, SCADA historians, medical modalities: unpatched vsftpd 2.x-era or embedded stacks, frequently with vendor-default accounts.

## Reading the protocol's mind

1. **`220` banner = product + version** before any auth — `220 (vsFTPd 3.0.5)`, `220---------- Welcome to Pure-FTPd [privsep] [TLS] ----------`, `220 Microsoft FTP Service`; gate your CVE claims on it.
2. **`331` vs `530` on USER is a username oracle** — per-product reply shapes differ; probe a known-valid and a random name to calibrate before brute.
3. **Anonymous maps to a directory somebody chose** — a share that lists anything was a deliberate choice; assume more where that came from and mirror it.
4. **PASV ports are ephemeral and often unrestricted** — the passive range must be open through the firewall; where it is not, admins enable active mode, which is where bounce-class behavior and firewall fun live.
5. **Passive vs active matters to your tooling** — `--no-passive-ftp` (wget), `ftp> passive`, or `lftp` semantics change which channel the data takes; wrong mode looks like a hang, not an error.

## Where to go next

[[ftp-vsftpd|vsftpd attack surface]] — the 2.3.4 backdoor (CVE-2011-2523), userlist/ftpusers deny behavior, and the config-audit method for the dominant implementation.

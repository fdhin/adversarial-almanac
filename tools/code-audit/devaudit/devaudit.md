---
name: "devaudit"
categories:
  - blackarch-code-audit
version: "803.ca0a68e"
homepage: "https://github.com/sonatype-nexus-community/DevAudit"
packaged-by: [blackarch]
blackarch-builddate: "2021-02-14"
build-system: other
license: BSD
meta-checked: "2026-11-09"
github-repo: "sonatype-nexus-community/DevAudit"
github-stars: 359
github-pushed: "2022-12-08"
github-archived: true
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# devaudit

> Cross-platform security auditor that checks installed packages, service configurations and .NET application code against OSS Index and Vulners data — locally or remotely over SSH, WinRM, Docker containers and GitHub repos.

*No corpus evidence — derived from upstream documentation (upstream repository is archived).*

## What it's for

DevAudit audits three kinds of targets. Package sources: OS packages (dpkg on Debian-family, rpm/yum on RedHat-family, MSI/Chocolatey/OneGet on Windows) and development dependencies (NuGet, npm, Bower, Composer). Application servers: sshd, Apache httpd, nginx, MySQL/MariaDB and PostgreSQL — both the server version and its configuration file, checked against YAML rule sets. Applications: ASP.NET/.NET configuration and CIL bytecode static analysis via external analyzers such as Gendarme. Vulnerability lookups run through the OSS Index and Vulners aggregators (rate-limited unauthenticated). The same audit can execute locally or against a remote environment — SSH, WinRM, a running Docker container, or a GitHub repository without checking it out. It is a .NET/Mono application and its upstream is archived (last push 2022), so treat it as a historical tool with a frozen vulnerability-data workflow.

## Typical use cases

### Audit all packages installed on a Debian-family host

```
devaudit dpkg -n
```

Outcome: enumerates every dpkg-installed package, resolves each against OSS Index and prints the vulnerabilities reported for those exact versions; `-n` disables the interactive UI so output can be piped or logged. Swap `dpkg` for `rpm` or `yum` on RedHat-family hosts.

### Audit a project's dependency manifest

```
devaudit composer -f /path/to/composer.json
```

Outcome: checks every dependency listed in the manifest for known vulnerabilities; the same `-f/--file` pattern applies to `nuget` (packages.config) and `bower`, while `npm` reads the package.json in the current directory.

### Check a service's version and configuration

```
devaudit sshd
```

Outcome: audits the local OpenSSH server — binary and config file are auto-detected when not specified — flagging both vulnerable versions and insecure configuration settings. `nginx`, `httpd`, `mysql` and `pgsql` work the same way; `--config-file` and `-b` point at non-default locations, and `--skip-packages-audit` restricts the run to configuration analysis.

### Audit a remote host over SSH

```
devaudit dpkg -s target.example.com -u user -k ~/.ssh/id_rsa
```

Outcome: runs the same package audit on target.example.com over SSH without DevAudit installed on the remote host; use `-p` for an interactive password prompt instead of a key file. On Windows the same pattern works over WinRM with `-w`.

### Preview what a target resolves to

```
devaudit npm --list-packages
```

Outcome: only lists the packages DevAudit sees in the package source — no vulnerability lookup — which is the quick way to confirm a target/environment combination resolves correctly; `--list-artifacts` additionally shows which of those packages OSS Index knows about.

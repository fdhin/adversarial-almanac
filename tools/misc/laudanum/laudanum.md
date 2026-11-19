---
name: "laudanum"
categories:
  - blackarch-misc
version: "1.0"
homepage: "http://laudanum.inguardians.com/#"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "junk13/laudanum"
github-stars: 0
github-pushed: "2018-05-31"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# laudanum

> Ready-to-deploy injectable web shells and helper pages (PHP, ASP, ASPX, JSP, CFM) for penetration tests.

## What it's for

Laudanum (Kevin Johnson, Tim Medin, Justin Searle and contributors, distributed via the SourceForge project and laudanum.inguardians.com) is a collection of small, single-purpose injectable files to drop on a web server once an upload flaw, an administrative interface or SQL injection gives a foothold. Each file does one thing — interactive command shell, file browser, DNS lookup, hostname lookup, HTTP proxy, netcat cleanup — with parallel implementations for PHP, classic ASP, ASPX, JSP (as a WAR), ColdFusion and a WordPress plugin variant. Every file ships with a hard-coded IP allow-list and sha1 password hashes that must be edited before use; requests from unlisted addresses get an HTTP 404, so a carelessly deployed droplet is inert and invisible rather than open. The files are deliberately minimal to blend in and remain auditable. Deploy them only against systems you are explicitly authorized to test.

GitHub: third-party mirror of the SourceForge/SVN upstream (sourceforge.net/projects/laudanum).

## Typical use cases

### Prepare a shell with your own allow-list and password

Edit `php/shell.php` before uploading so only your address and account can reach it.

```
# hash the password to paste into the $users array
echo -n 'Password1' | sha1sum
```

Outcome: the sha1 hash for `$users = array("user" => "<hash>")`; also set `$allowedIPs` to your source IP. Upload the file through the target's flaw; other visitors receive 404, while you get the login form and an interactive shell running as the web server account.

### Read files through the compromised host

Use the file-browser droplet with a path of interest.

```
curl 'http://target.example.com/uploads/file.php?file=/etc/hosts'
```

Outcome: the requested file's contents as the web server account sees them — useful for config harvesting and confirming read access outside the web root (`dir=` browses directories).

### Resolve names from the target's point of view

Ask the DNS droplet to query through the target's resolver.

```
curl -d 'query=example.com&type=A' 'http://target.example.com/uploads/dns.php'
```

Outcome: the DNS answer the compromised host receives, exposing internal split-horizon records and egress-filtered resolvers that an external lookup cannot see; `type` selects the record class (A, MX, ...).

### Pivot through the target with the HTTP proxy

Deploy `php/proxy.php` like any other droplet and open it in a browser.

```
curl 'http://target.example.com/uploads/proxy.php'
```

Outcome: the proxy page loads; URLs submitted through its form are fetched by the target server itself, making internal-only sites reachable from your workstation.

### Get command execution on a JSP container

Build the ready-made WAR wrapper around the JSP command shell.

```
cd jsp && ./makewar.sh
```

Outcome: `cmd.war` is assembled from `warfiles/` (requires the `jar` tool from a JDK); deploy it via the Tomcat manager and request `/cmd/cmd.jsp` for a command page running inside the application server.

*No corpus evidence — derived from upstream documentation.*

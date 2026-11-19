---
name: "absinthe"
gentoo-category: "dev-db"
version: "1.4.1"
homepage: "http://www.0x90.org/releases/absinthe/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: || ( GPL-2 BSD )
meta-checked: "2026-11-09"
github-repo: "cameronhotchkies/Absinthe"
github-stars: 80
github-pushed: "2012-07-31"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# absinthe

> GUI tool by nummish (0x90.org) that automates SQL injection data extraction — blind boolean injection against Oracle, MS SQL Server and PostgreSQL, plus faster error-based injection against MS SQL Server.

## What it's for

Absinthe extracts data from web applications that are vulnerable to SQL injection but hide database error messages. It is a desktop application written in C# on wx.NET and runs under Mono; there is no command-line mode — target URL, cookies, proxy settings and the injection strategy are all entered in its windows. It performs blind TRUE/FALSE injection against Oracle, MS SQL Server and PostgreSQL, and from version 1.4 also error-based injection against MS SQL Server, which is much faster when the application echoes database errors. It handles SSL targets, HTTP proxies and custom User-Agent strings, downloads whole tables and can resume partial downloads. It is not the Absinthe iOS jailbreak tool and not the Elixir GraphQL library of the same name.

GitHub: third-party mirror of nummish's Absinthe (0x90.org).

## Typical use cases

### Launch the injection GUI

Start the application from an unpacked release directory (distro packages wrap the same call as `absinthe`).

```
mono Absinthe.exe
```

Outcome: opens the Absinthe GUI window. You enter the vulnerable URL (it switches to `https://` automatically for SSL targets), set cookies/proxy/User-Agent as needed, choose between blind and MS SQL error-based injection, and drive table discovery and data extraction from the interface; downloaded data is saved in a documented XML format.

*No corpus evidence — derived from upstream documentation.*

---
name: "nosqlattack"
categories:
  - blackarch-automation
version: "98.a5b0329"
homepage: "https://github.com/youngyangyang04/NoSQLAttack"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "youngyangyang04/NoSQLAttack"
github-stars: 316
github-pushed: "2024-04-08"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nosqlattack

> Menu-driven Python tool that automates attacks on exposed MongoDB servers and NoSQL-injectable web applications.

## What it's for

NoSQLAttack (youngyangyang04) bundles two MongoDB attack families behind a numbered console menu: default-configuration attacks against MongoDB instances left without authentication or bound publicly (enumerate and dump their databases), and NoSQL injection attacks (PHP array injection, JavaScript injection, mongo-shell injection) against a target web application, based on published research papers. Target and option setup happens through the menu's configuration section — including a Shodan key for discovering exposed MongoDB IPs — rather than command-line flags. It is Python 2 era software (2017) and MongoDB-focused; modern MongoDB installs ship with authentication enabled by default, so treat the default-config attacks as verification that an instance is genuinely exposed, and expect to fight old dependencies when running it.

## Typical use cases

### Launch the console and configure a target

Start the tool and set attack parameters.

```
nosqlattack
```

Outcome: prints the NoSQLAttack banner and version, then the main menu — `1` scan the attacked IP, `2` configure parameters, `3` MongoDB access attacks, `4` injection attacks, `x` exit. Use option `2` first to store the target IP/URL and, if you want internet-wide discovery, the Shodan API key; options persist for the session and are required before options 3 and 4 will run.

### Attack an exposed MongoDB's default configuration

Enumerate a no-auth instance.

```
nosqlattack
```

then pick menu option `3` after configuring the target with option `2` (a local MongoDB install is needed as the attack pivots through it).

Outcome: the tool connects to the target MongoDB that is running with default settings (no auth, exposed port), enumerates its databases and collections, and discloses the stored data — demonstrating concretely that the instance relies on its default configuration.

### Run NoSQL injection attacks against a web application

Test injection vectors on a target URL.

```
nosqlattack
```

then pick menu option `4` with the target web application configured through option `2`.

Outcome: NoSQLAttack fires its PHP-array/JavaScript/mongo-shell injection payloads at the configured target URL and reports which parameters and injection classes the application is vulnerable to; the companion project NoSQLInjectionAttackDemo provides deliberately vulnerable apps to test against.

*No corpus evidence — derived from upstream documentation.*

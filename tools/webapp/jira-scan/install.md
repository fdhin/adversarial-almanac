---
tool: "jira-scan"
build-system: "ruby"
upstream: "https://github.com/bcoles/jira_scan"
recipe: "blackarch"
source: "https://github.com/bcoles/jira_scan"
license: "MIT"
blackarch-pkgver: "7.447d0ec"
---

# Installing jira-scan

> A simple remote scanner for Atlassian Jira

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jira-scan) (version `7.447d0ec`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S jira-scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

```bash
git clone https://github.com/bcoles/jira_scan.git
cd jira_scan
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-pkg-config`
  - `ruby-terminal-table`

## Exact build steps from the recipe

```bash
cd $pkgname

  gem build "$_gemname.gemspec"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jira-scan]] · [[BlackArch Tools]]


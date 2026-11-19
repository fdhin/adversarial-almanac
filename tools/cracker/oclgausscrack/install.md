---
tool: "oclgausscrack"
build-system: "make"
upstream: "https://github.com/jsteube/oclGaussCrack"
recipe: "kali-debian"
version: "1.3-1kali3"
---

# Installing oclgausscrack

> Cracks verification hashes of the Gauss Virus The goal of the program is to crack the verification hash of the encrypted payload of the Gauss Virus. .    Uses OpenCL to accelerate the 10k MD5 loop    Uses optimizations also used in oclHashcat-plus for maximum performance    Able to handle multi-GPU setups (of the same type)    VCL (Virtual CL) v1.18 compatible    Open Source    Supports integration into distributed computing environments    Supports resume

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/oclgausscrack) (version `1.3-1kali3`).

## Kali / Debian-based

```bash
sudo apt install oclgausscrack
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss oclgausscrack`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/jsteube/oclGaussCrack
cd oclGaussCrack
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/oclgausscrack](https://pkg.kali.org/pkg/oclgausscrack)

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_clean:
override_dh_auto_build:
override_dh_auto_install:
override_dh_shlibdeps:
```

Backlinks: [[cracker]] · [[oclgausscrack]] · [[Kali Gap Analysis]]

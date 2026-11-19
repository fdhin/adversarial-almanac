---
tool: "hyperion"
build-system: "windows"
upstream: "http://www.nullsecurity.net/tools/binary.html"
recipe: "kali-debian"
version: "2.0-0kali4"
---

# Installing hyperion

> Runtime encrypter for 32-bit portable executables This package contains a runtime encrypter for 32-bit portable executables. It is a reference implementation and bases on the paper "Hyperion: Implementation of a PE-Crypter". The paper describes the implementation details which aren't in the scope of this readme file. The crypter is started via the command line and encrypts an input executable with AES-128. The encrypted file decrypts itself on startup (bruteforcing the AES key which may take a few seconds) and generates a log file for debug purpose.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/hyperion) (version `2.0-0kali4`).

## Kali / Debian-based

```bash
sudo apt install hyperion
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss hyperion`); otherwise build from source below.

## Any other Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.nullsecurity.net/tools/binary.html
wine <tool>.exe
```

## Dependencies (from debian/control)

- Build (Debian names): `gcc-mingw-w64-i686`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[windows]] · [[hyperion]] · [[Kali Gap Analysis]]

---
name: "cryptohaze-combined"
gentoo-category: "app-crypt"
version: "9999"
homepage: "http://www.cryptohaze.com/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: cmake
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cryptohaze-combined

> Gentoo/pentoo source build of the dormant Cryptohaze GPU password-auditing suite — the CUDA/OpenCL Multiforcer plus GPU rainbow-table tools.

## What it's for

This ebuild compiles the Cryptohaze-Combined sources (fetched from the project's Subversion repository or its SourceForge release tarballs) rather than shipping a prebuilt binary. The build produces the same `Cryptohaze-Multiforcer` binary that BlackArch distributes prebuilt as the separate `cryptohazemultiforcer` package, and — when the `grt` USE flag is set — the Cryptohaze GPU rainbow-table (GRT) tools, which use much longer chains (200,000 vs RainbowCrack's 10,000) to cover large plaintext spaces such as full-charset length-8 NTLM. Wrappers for every built executable are installed onto the PATH, warning that they run from the share directory so relative paths apply. The upstream project (cryptohaze.com, SourceForge) has been inactive since around 2015, and the OpenCL rewrite of the era (Multiforcer New / MFN) was never released here; for current GPU cracking, maintained hashcat or John the Ripper OpenCL builds are the practical choice.

The Multiforcer command-line itself — hash types, charset files, length ranges, and the network server/client mode — is documented in the sibling note [[cryptohazemultiforcer]], which packages the identical binary; refer there for usage rather than duplicating it.

*No corpus evidence — derived from upstream documentation.*

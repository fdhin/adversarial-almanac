---
name: "osslsigncode"
categories:
  - blackarch-windows
version: "750.868d6e9"
homepage: "https://github.com/mtrojnar/osslsigncode"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mtrojnar/osslsigncode"
github-stars: 1116
github-pushed: "2026-11-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 82
gh-activity-checked: "2026-11-09"
---

# osslsigncode

> OpenSSL-based implementation of Microsoft's signtool.exe for Authenticode signing, timestamping, verifying and stripping signatures.

## What it's for

osslsigncode reproduces the parts of signtool.exe you need for Authenticode — signing and timestamping — on any platform where OpenSSL and curl build, so you can sign Windows binaries from Linux/CI without a Windows host. It handles PE (EXE/SYS/DLL), CAB, CAT, MSI and APPX files plus PowerShell script types (`.ps1`, `.ps1xml`, `.psc1`, `.psd1`, `.psm1`, `.cdxml`, `.mof`) and `.js`, and also verifies, extracts and removes signatures. Keys can come from PEM/DER/PVK files, a PKCS#12 container, or a PKCS#11 token/HSM (engine or OpenSSL 3 provider). It does not obtain certificates for you — you bring the code-signing cert and key.

## Typical use cases

### Sign a PE file with a certificate and key

Attach an Authenticode signature to a Windows executable.

```
osslsigncode sign -certs codesign.pem -key codesign.key -n "Your Application" -i https://www.example.com/ -in app.exe -out app-signed.exe
```

Outcome: writes app-signed.exe carrying your signature with the supplied program name and URL; on Windows, Properties → Digital Signatures on the file shows the signature as valid.

### Sign from a PKCS#12 container with a timestamp

Use a `.pfx` bundle and add an RFC 3161 timestamp so the signature outlives certificate expiry.

```
osslsigncode sign -pkcs12 codesign.pfx -pass Password1 -n "Your Application" -i https://www.example.com/ -t http://timestamp.digicert.com -in app.exe -out app-signed.exe
```

Outcome: writes the signed executable with a counter-signature timestamp from the DigiCert timestamp server, verifiable even after the signing certificate expires.

### Sign with a key held in an HSM

Drive a PKCS#11 token (engine or OpenSSL 3 provider) instead of a key file.

```
osslsigncode sign -provider /usr/lib64/ossl-modules/pkcs11prov.so -pkcs11module /usr/lib64/opensc-pkcs11.so -pkcs11cert 'pkcs11:token=my-token;object=cert' -key 'pkcs11:token=my-token;object=key' -in app.exe -out app-signed.exe
```

Outcome: signs app.exe using the certificate and private key on the token; the key never leaves the HSM.

### Verify an existing signature

Check whether a file's signature is intact and chains correctly.

```
osslsigncode verify -in app-signed.exe
```

Outcome: prints the signature and certificate chain details with the verification result, or reports that no signature is present / verification failed.

### Extract or strip a signature

Recover the signature blob for analysis, or produce an unsigned copy.

```
osslsigncode extract-signature -in app-signed.exe -out signature.pem
osslsigncode remove-signature -in app-signed.exe -out app-unsigned.exe
```

Outcome: the first command writes the detached signature (PKCS#7) to signature.pem for inspection; the second writes a clean unsigned copy of the binary.

*No corpus evidence — derived from upstream documentation (README plus subcommands verified in the osslsigncode.c source).*

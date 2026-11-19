---
name: "aespipe"
categories:
  - blackarch-crypto
version: "2.4i"
homepage: "http://loop-aes.sourceforge.net/aespipe/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-04-21"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/aespipe"
github-stars: 2
github-pushed: "2019-05-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aespipe

> AES encrypting/decrypting pipe — reads plaintext on stdin and writes AES-CBC ciphertext to stdout, in a format compatible with loop-AES encrypted disk images.

## What it's for

aespipe (from the loop-AES project) encrypts or decrypts a byte stream through a pipeline: data goes in on stdin, AES-CBC comes out on stdout, in 512-byte chains. That makes it the natural tool for encrypted tar/cpio archives and for creating or reading loop-AES compatible encrypted disk and CD-ROM images. It supports three key setups: single-key mode with a typed passphrase, and multi-key-v2/v3 modes driven by a gpg-encrypted key file of 64 or 65 keys (the recommended setup, because gpg adds salting and key iteration against dictionary attacks). It is block-oriented, not a general-purpose file encryptor: output size is padded to a multiple of 16 bytes (single-key) or 512 bytes (multi-key), so for arbitrary file encryption GnuPG is the better fit. It decrypts only what aespipe/loop-AES encrypted.

## Typical use cases

### Create and restore an encrypted tar archive with a passphrase

```
tar cvf - docs/ | aespipe -e AES256 > archive.aes
```

```
aespipe -d -e AES256 < archive.aes | tar xvpf -
```

Outcome: aespipe prompts on the terminal for a passphrase (use 20+ characters), then encrypts the tar stream with AES-256-CBC; `-d` reverses it into a tar stream for extraction. The first command writes a self-contained encrypted archive; the second restores the files into the current directory.

### Create and restore an archive with a gpg-encrypted multi-key file

```
head -c 3705 /dev/random | uuencode -m - | head -n 66 | tail -n 65 | gpg --symmetric -a > mykey1.gpg
tar cvf - docs/ | bzip2 | aespipe -w 10 -K mykey1.gpg > archive.aes
```

```
aespipe -d -K mykey1.gpg < archive.aes | bzip2 -d -q | tar xvpf -
```

Outcome: the first pipeline builds a gpg-symmetric-encrypted file holding 65 random keys (multi-key-v3 mode, the recommended setup). aespipe then pipes the passphrase through gpg to decrypt `mykey1.gpg` internally — `-w 10` waits 10 seconds first so the passphrase prompt doesn't appear while terminal noise from tar is still scrolling — and uses those keys for the actual encryption. Decrypt with the mirrored pipeline.

### Build a loop-AES compatible encrypted ISO image

```
mkisofs -quiet -r directory-tree | aespipe -K image.iso -O 16 >> image.iso
```

Outcome: appends AES-encrypted ISO9660 data to `image.iso` after a 16-sector (8192-byte) header region reserved for the gpg-encrypted key file, producing an image mountable by loop-AES with options like `loop=/dev/loop0,encryption=AES128,gpgkey=image.iso,offset=8192`. To recover a clear image from such media: `dd if=/dev/cdrom bs=8192 skip=1 | aespipe -d -K key.gpg -O 16 > clear.iso`, where `key.gpg` was first cut out with `dd if=/dev/cdrom bs=8192 count=1 of=key.gpg`.

### Harden the passphrase with a seed and key iteration

```
tar cvf - docs/ | aespipe -e AES256 -S myseed -C 10 > archive.aes
```

Outcome: `-S` appends a seed to the typed passphrase before hashing and `-C 10` runs the hashed passphrase through 10,000 extra AES-256 iterations, both of which slow down offline dictionary attacks against a captured archive. These options apply to single-key mode only; in multi-key mode the gpg key file already provides the protection. `-v` prints diagnostics about key length and single/multi-key mode to stderr if you want to confirm which setup took effect.

*No corpus evidence — derived from upstream documentation.*

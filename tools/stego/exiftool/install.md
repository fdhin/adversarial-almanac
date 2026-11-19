---
tool: "exiftool"
build-system: "perl"
upstream: "https://github.com/exiftool/exiftool"
recipe: "upstream"
license: "GPL-3.0"
---

# Installing exiftool

> Reads, writes and strips metadata in 150+ file types — the standard first probe for hidden data in images, documents and media.

Source: hacktricks feed; upstream [`exiftool/exiftool`](https://github.com/exiftool/exiftool) (canonical home and docs: [exiftool.org](https://exiftool.org/)).

## Distro packages

```bash
pacman -S perl-image-exiftool    # Arch
apt install libimage-exiftool-perl   # Debian/Kali
dnf install perl-Image-ExifTool  # Fedora
```

## From the upstream distribution

```bash
gzip -dc Image-ExifTool-13.63.tar.gz | tar -xf -
cd Image-ExifTool-13.63
./exiftool t/images/ExifTool.jpg    # runs as-is, no install needed
```

Install as a Perl module for other scripts:

```bash
perl Makefile.PL
make
make test
sudo make install
```

## Notes

Requires only Perl ≥ 5.004; optional Perl modules (Archive::Zip,
Compress::Zlib, Digest::SHA...) unlock ZIP/DOCX/PDF/AES features — the
distro packages pull the important ones in.

Backlinks: [[stego]] · [[exiftool]]

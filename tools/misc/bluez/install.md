---
tool: "bluez"
build-system: "autotools"
upstream: "http://www.bluez.org/"
recipe: "arch-official/extra"
nix-package: "bluez"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/bluez/-/raw/main/PKGBUILD"
version: "5.88"
license: "GPL-2.0-only"
---

# Installing bluez

> Bluetooth tools and daemons

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/bluez/-/raw/main/PKGBUILD) (version `5.88`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S bluez
```

## Kali / Debian-based — same package name

```bash
sudo apt install bluez
```

## Nix (works on any distro)

```bash
nix run nixpkgs#bluez    # ad-hoc run, no install
nix profile install nixpkgs#bluez   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/bluez/bluez.git?signed
cd bluez.git?signed
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Python (pip)**: `docutils`, `pygments`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `cups`
  - `dbus`
  - `ell`
  - `json-c`
  - `libical`
  - `systemd`

## Exact build steps from the recipe

```bash
cd "${pkgname}"
  ICAL_LIBS="-lical -licalvcal" \
  ./configure \
          --prefix=/usr \
          --mandir=/usr/share/man \
          --sysconfdir=/etc \
          --localstatedir=/var \
          --libexecdir=/usr/lib \
          --with-dbusconfdir=/usr/share \
          --enable-btpclient \
          --enable-midi \
          --enable-sixaxis \
          --enable-mesh \
          --enable-hid2hci \
          --enable-experimental \
          --enable-datafiles \
          --enable-external-ell \
          --enable-library --enable-deprecated # libraries and these tools are deprecated
  make

  # fake installation to be seperated into packages
  make DESTDIR="${srcdir}/fakeinstall" install

  # add missing tools FS#41132, FS#41687, FS#42716
  for files in `find tools/ -type f -perm -755`; do
    filename=$(basename $files)
    install -Dm755 "${srcdir}"/"${pkgbase}"/tools/$filename "${srcdir}/fakeinstall"/usr/bin/$filename
  done

  for files in `find client/btpclient/ -type f -perm -755`; do
    filename=$(basename $files)
    install -Dm755 "${srcdir}"/"${pkgbase}"/client/btpclient/$filename "${srcdir}/fakeinstall"/usr/bin/$filename
  done
  

  # add man pages for the above tools
  # https://github.com/bluez/bluez/commit/44e3dd321e4be052bcde40939552b93b734538d3
  for manfile in `find doc/ -type f -regex '.*\.[1-8]'`; do
    section=$(echo $manfile | sed -E 's/.*\.([1-8])$/\1/')
    filename=$(basename $manfile)
    install -Dm644 "${srcdir}"/"${pkgbase}"/doc/$filename "${srcdir}/fakeinstall"/usr/share/man/man${section}/$filename
  done
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[misc]] · [[bluez]] · [[Kali Gap Analysis]]

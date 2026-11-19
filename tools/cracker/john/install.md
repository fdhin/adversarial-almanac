---
tool: "john"
build-system: "autotools"
upstream: "https://www.openwall.com/john"
recipe: "arch-official/extra"
nix-package: "john"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/john/-/raw/main/PKGBUILD"
version: "${_johnver}.jumbo${_jumbover}"
license: "GPL2, custom"
---

# Installing john

> active password cracking tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/john/-/raw/main/PKGBUILD) (version `${_johnver}.jumbo${_jumbover}`, license GPL2, custom).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S john
```

## Kali / Debian-based — same package name

```bash
sudo apt install john
```

## Nix (works on any distro)

```bash
nix run nixpkgs#john    # ad-hoc run, no install
nix profile install nixpkgs#john   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/openwall/john) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gmp`
  - `libcrypt.so`
  - `libgcc`
  - `libgomp`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libxcrypt`
  - `opencl-headers`
  - `opencl-icd-loader`
  - `openmpi`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pkg-config`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${_johnver}-jumbo-${_jumbover}/src

  CFLAGS+=' -fcommon' # https://wiki.gentoo.org/wiki/Gcc_10_porting_notes/fno_common

  local CFLAGS="${CFLAGS} -DCPU_FALLBACK"
  local options=(
    --prefix=/usr
    --with-systemwide
    --disable-native-tests
    --enable-openmp
    --enable-mpi
    --enable-opencl
    --enable-pkg-config
    --enable-pcap
  )

  if [[ "${CARCH}" == "x86_64" ]]; then
    ./configure "${options[@]}" CFLAGS="${CFLAGS/-DCPU_FALLBACK}"
    make clean; make
    mv ../run/john{,-non-avx}
    ./configure "${options[@]}" CFLAGS="${CFLAGS} -mavx"
    make clean; make
    mv ../run/john{,-non-xop}
    ./configure "${options[@]}" CFLAGS="${CFLAGS} -mxop"
    make clean; make
  elif [[ "${CARCH}" == "i686" ]]; then
    ./configure "${options[@]}" CFLAGS="${CFLAGS}"
    make clean; make
    mv ../run/john{,-non-mmx}
    ./configure "${options[@]}" CFLAGS="${CFLAGS} -mmmx"
    make clean; make
    mv ../run/john{,-non-sse}
    ./configure "${options[@]}" CFLAGS="${CFLAGS} -msse2"
    make clean; make
    mv ../run/john{,-non-avx}
    ./configure "${options[@]}" CFLAGS="${CFLAGS} -mavx"
    make clean; make
    mv ../run/john{,-non-xop}
    ./configure "${options[@]}" CFLAGS="${CFLAGS} -mxop"
    make clean; make
  else
    ./configure "${options[@]}" CFLAGS="${CFLAGS}"
    make clean; make
  fi
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[john]] · [[Kali Gap Analysis]]

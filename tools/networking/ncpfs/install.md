---
tool: "ncpfs"
build-system: "autotools"
upstream: "http://www.novell.com/"
recipe: "blackarch"
source: "http://www.novell.com/"
license: "GPL"
blackarch-pkgver: "2.2.6"
---

# Installing ncpfs

> Allows you to mount volumes of NetWare servers under Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ncpfs) (version `2.2.6`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ncpfs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.novell.com/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `linux`
  - `pam`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  patch -p1 -i "$srcdir/01-linux3.0_uname.patch"
  patch -p1 -i "$srcdir/02-legacy.patch"
  patch -p1 -i "$srcdir/03-multiple_security_issues.patch"

  # fix glibc 2.16.0-2
  export ncp_cv_pam_directory='/usr/lib/security'

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    if [ $_with_php -eq 1 ]; then
      ./configure --prefix=/usr --mandir=/usr/share
      sed 's/function_entry/zend_function_entry/' -i contrib/php/php_auth_nds.c
    else
      ./configure --prefix=/usr --mandir=/usr/share --disable-php
    fi
  else
    if [ $_with_php -eq 1 ]; then
      ./configure --prefix=/usr --mandir=/usr/share --build=arm
      sed 's/function_entry/zend_function_entry/' -i contrib/php/php_auth_nds.c
    else
      ./configure --prefix=/usr --mandir=/usr/share --disable-php --build=arm
    fi
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`, `/usr/share/man1`, `/usr/share/man3`, `/usr/share/man8`

Backlinks: [[networking]] · [[ncpfs]] · [[BlackArch Tools]]


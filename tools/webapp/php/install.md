---
tool: "php"
build-system: "autotools"
upstream: "https://www.php.net/"
recipe: "arch-official/extra"
nix-package: "php"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/php/-/raw/main/PKGBUILD"
version: "8.5.12"
license: "PHP-3.01"
---

# Installing php

> server-side, HTML-embedded scripting language (default)

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/php/-/raw/main/PKGBUILD) (version `8.5.12`, license PHP-3.01).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S php
```

## Kali / Debian-based — same package name

```bash
sudo apt install php
```

## Nix (works on any distro)

```bash
nix run nixpkgs#php    # ad-hoc run, no install
nix profile install nixpkgs#php   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.php.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `apache`
  - `argon2`
  - `curl`
  - `enchant`
  - `freetds`
  - `gd`
  - `gmp`
  - `icu`
  - `libsodium`
  - `libtiff`
  - `libtool`
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `libzip`
  - `net-snmp`
  - `oniguruma`
  - `pcre2`
  - `postfix`
  - `postgresql-libs` — Debian libpq-dev / Fedora libpq-devel
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `systemd`
  - `tidy`
  - `unixodbc`

## Exact build steps from the recipe

```bash
local _phpconfig="--srcdir=../${pkgbase}-${pkgver} \
		--config-cache \
		--prefix=/usr \
		--sbindir=/usr/bin \
		--sysconfdir=/etc/php \
		--localstatedir=/var \
		--with-layout=GNU \
		--with-config-file-path=/etc/php \
		--with-config-file-scan-dir=/etc/php/conf.d \
		--disable-rpath \
		--mandir=/usr/share/man \
		"

	local _phpextensions="\
		--enable-bcmath=shared \
		--enable-calendar=shared \
		--enable-dba=shared \
		--enable-exif=shared \
		--enable-ftp=shared \
		--enable-gd=shared \
		--enable-intl=shared \
		--enable-mbstring \
		--enable-pcntl \
		--enable-shmop=shared \
		--enable-soap=shared \
		--enable-sockets=shared \
		--enable-sysvmsg=shared \
		--enable-sysvsem=shared \
		--enable-sysvshm=shared \
		--with-bz2=shared \
		--with-curl=shared \
		--with-enchant=shared \
		--with-external-gd \
		--with-external-pcre \
		--with-ffi=shared \
		--with-gdbm \
		--with-gettext=shared \
		--with-gmp=shared \
		--with-iconv=shared \
		--with-ldap=shared \
		--with-ldap-sasl \
		--with-mhash \
		--with-mysql-sock=/run/mysqld/mysqld.sock \
		--with-mysqli=shared \
		--with-openssl \
		--with-openssl-argon2 \
		--with-password-argon2 \
		--with-pdo-dblib=shared,/usr \
		--with-pdo-mysql=shared \
		--with-pdo-odbc=shared,unixODBC,/usr \
		--with-pdo-pgsql=shared \
		--with-pdo-sqlite=shared \
		--with-pgsql=shared \
		--with-readline \
		--with-snmp=shared \
		--with-sodium=shared \
		--with-sqlite3=shared \
		--with-tidy=shared \
		--with-unixODBC=shared \
		--with-xsl=shared \
		--with-zip=shared \
		--with-zlib \
		"

	EXTENSION_DIR=/usr/lib/php/modules
	export EXTENSION_DIR

	mkdir "${srcdir}/build"
	cd "${srcdir}/build"
	ln -s "../${pkgbase}-${pkgver}/configure"
	./configure ${_phpconfig} \
		--enable-cgi \
		--enable-fpm \
		--with-fpm-systemd \
		--with-fpm-acl \
		--with-fpm-user=http \
		--with-fpm-group=http \
		--enable-embed=shared \
		${_phpextensions}
	make

	# apache
	# reuse the previous run; this will save us a lot of time
	cp -a "${srcdir}/build" "${srcdir}/build-apache"
	cd "${srcdir}/build-apache"
	./configure ${_phpconfig} \
		--with-apxs2 \
		${_phpextensions}
	make

	# phpdbg
	cp -a "${srcdir}/build" "${srcdir}/build-phpdbg"
	cd "${srcdir}/build-phpdbg"
	./configure ${_phpconfig} \
		--enable-phpdbg \
		${_phpextensions}
	make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[php]] · [[Kali Gap Analysis]]

---
name: "hping-install"
categories:
  - networking
---

# Installing hping

Packaged everywhere (development is frozen since 2014 — prefer distro builds over source):

```bash
sudo pacman -S hping        # Arch (extra)
sudo apt install hping3     # Debian / Ubuntu / Kali
nix run nixpkgs#hping       # Nix, ad-hoc
```

Runtime deps per the Debian package: libpcap and libtcl8.6 (the TCL scripting mode). From source (last resort — the tree is a 2014 autotools build):

```bash
git clone https://github.com/antirez/hping
cd hping
./configure
make
sudo make install
```

The installed command is `hping3` (`hping` on Arch/nix); raw sockets mean it runs under sudo/root or with `cap_net_raw` on the binary.

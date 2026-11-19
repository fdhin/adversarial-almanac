---
tool: "jmsdigger-bin"
build-system: "prebuilt"
upstream: "https://github.com/OpenSecurityResearch/jmsdigger"
recipe: "pentoo-ebuild"
version: "20130815"
license: "Apache-2.0"
---

# Installing jmsdigger-bin

> JMSDigger is an Enterprise Messaging Application assessment tool focuses on ActiveMQ

Recipe source of truth: Pentoo [`jmsdigger-bin.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/jmsdigger-bin) (version `20130815`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask jmsdigger-bin
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss jmsdigger-bin`).

## Any other Linux distro


## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `virtual/jdk`, `virtual/jre`

Backlinks: [[scanner]] · [[jmsdigger-bin]] · [[Pentoo Gap Analysis]]

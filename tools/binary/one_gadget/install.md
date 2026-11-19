---
tool: "one_gadget"
build-system: "ruby"
upstream: "https://github.com/david942j/one_gadget"
recipe: "upstream"
license: "MIT"
---

# Installing one_gadget

> Finds the magic offsets in a libc that jump straight to `execve("/bin/sh", ...)` — with the exact register constraints each one needs.

Source: hacktricks feed; upstream [`david942j/one_gadget`](https://github.com/david942j/one_gadget).

## From RubyGems

```bash
gem install one_gadget
```

## Distro package

```bash
pacman -S one-gadget     # Arch
```

## Notes

A Ruby gem (Ruby ≥ 2.4 suggested) with no native extensions; gadget
databases for common glibc builds ship inside the gem, others are fetched
on demand by BuildID.

Backlinks: [[binary]] · [[one_gadget]]

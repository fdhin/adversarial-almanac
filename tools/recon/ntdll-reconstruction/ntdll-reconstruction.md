---
name: "ntdll-reconstruction"
categories:
  - recon
version: "git (2026-10-28)"
homepage: "https://github.com/sk4r4b/ntdll-reconstruction"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "sk4r4b/ntdll-reconstruction"
github-stars: 37
github-pushed: "2026-10-28"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# ntdll-reconstruction

> Userland EDR-bypass research library: flips a process between a clean (unhooked) and the hooked ntdll.dll mapping at will.

## What it's for

NTDLL-reconstruction (sk4r4b, 2026) is a Windows evasion technique published as a small C library rather than a command-line tool: a header and `.c` file you drop into your own project. It combines classic NTDLL unhooking (re-acquiring a pristine copy of ntdll.dll, e.g. from a known-good section on disk, and mapping its .text over the hooked one) with a HellsGate-style dynamic syscall gate so calls continue to resolve correctly in either mapping. The advertised payoff is *reversibility*: instead of unhooking once and staying unhooked (a state an EDR can detect), you flip to the clean stub, run the sensitive syscall, and flip back to the hooked stub, so the process spends most of its life looking normal. It is research/red-team material, not a ready-made executable — you must supply the `switch_memory_protection` implementation and reimplement or choose CRT functions used by the library, as the README's integration notes spell out. Of no use outside Windows userland offensive work; for defenders it documents a hook-state toggle pattern worth monitoring.

## Typical use cases

### Integrate the flip mechanism into a project

Adopt the library as documented by upstream.

```c
struct hook_cache *cache = 0;

cache = ntdll_reconstruction_init();
if (cache == 0)
    /* throw error */;

while (1) {
    cache = ntdll_reconstruction_flip(cache); /* back to hooked stub */
    if (cache == 0)
        /* throw error */;
    Sleep(1234);
    cache = ntdll_reconstruction_flip(cache); /* back to clean stub */
    if (cache == 0)
        /* throw error */;
}
```

Outcome: `ntdll_reconstruction_init()` builds the clean ntdll mapping cache and returns a `hook_cache`; each `ntdll_reconstruction_flip(cache)` toggles the process's ntdll text between the reconstructed (unhooked) mapping and the original hooked one, returning the updated cache or 0 on failure — the documented loop shape keeps a payload flipping between states instead of living unhooked. Copy `ntdll_reconstruction.h`/`.c` into the build, implement `switch_memory_protection`, and satisfy the CRT declarations the header expects before it compiles.

*No corpus evidence — derived from upstream documentation (API and integration requirements from the repository README; this is a source library with no CLI).*

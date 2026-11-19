---
name: "ctypes-sh"
categories:
  - blackarch-reversing
version: "165.ca66ae5"
homepage: "https://github.com/taviso/ctypes.sh"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-08-02"
build-system: autotools
license: MIT
meta-checked: "2026-11-09"
github-repo: "taviso/ctypes.sh"
github-stars: 2181
github-pushed: "2026-07-21"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# ctypes-sh

> A bash plugin that gives shell scripts a foreign-function interface — call routines in any shared library directly from the command line.

## What it's for

ctypes.sh (by taviso) loads new builtins into bash through libffi: `dlopen`/`dlclose`/`dlsym` for library handling, `dlcall` to invoke functions with typed arguments and return values, `pack`/`unpack` to move data between bash arrays and native memory, `callback` to expose bash functions as C function pointers, and `struct`/`sizeof` helpers when built with debug-info support. For reversing and exploitation work it enables quick experiments against a library's exported functions — libc, libm or a target's own shared object — without writing a C helper. Upstream installs the plugin as `ctypes.sh` on the PATH; the BlackArch package installs it as `/usr/bin/ctypes-sh`.

## Typical use cases

### Call a libc function from bash

Load the plugin and invoke a library routine.

```
source /usr/bin/ctypes-sh
dlcall puts "hello, world"
```

Outcome: bash gains the ctypes builtins and the string is printed by libc's puts — no external helper process involved.

### Call a typed function in libm

Open a specific library and call it with argument and return types.

```
source /usr/bin/ctypes-sh
dlopen libm.so.6
dlcall -r double sin double:1.57079632679489661923
```

Outcome: returns and prints `double:1.000000` — sin(π/2) computed by the real libm, with the calling convention handled by the plugin.

### Capture a return value in a variable

Store the typed result instead of printing it.

```
source /usr/bin/ctypes-sh
dlopen libm.so.6
dlcall -n result -r double sin double:0.7
echo "${result##*:}"
```

Outcome: `$result` holds `double:0.644218` in type-prefixed form; stripping the prefix leaves the bare number for shell use.

### Pass a bash function as a C callback

Sort a native array with libc qsort using a comparator written in bash.

```
source /usr/bin/ctypes-sh
values=(int:42 int:13 int:7)
dlcall -n buf -r pointer malloc 12
pack "$buf" values
cmp () {
    local -a x=(int) y=(int) r=(int)
    unpack "$2" x; unpack "$3" y
    x=${x##*:}; y=${y##*:}
    r=(int:$((x - y)))
    pack "$1" r
}
callback -n cmp cmp int pointer pointer
dlcall qsort "$buf" long:3 long:4 "$cmp"
unpack "$buf" sorted
echo "${sorted[*]}"
```

Outcome: prints `int:7 int:13 int:42` — libc's qsort sorted the malloc'd native array by calling back into the bash function through a generated function pointer.

*No corpus evidence — derived from upstream documentation.*

---
name: "sloth-fuzzer"
categories:
  - blackarch-fuzzer
version: "39.9f7f59a"
homepage: "https://github.com/mfontanini/sloth-fuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2016-02-14"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mfontanini/sloth-fuzzer"
github-stars: 26
github-pushed: "2016-08-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sloth-fuzzer

> Template-based file fuzzer: generates structurally valid files from a compact format description and crashes targets with them.

## What it's for

sloth (Manuel Fontanini; the upstream binary is `sloth`, packaged by BlackArch as `sloth-fuzzer`) is a "smart" file fuzzer. Instead of feeding random bytes, you describe the target file format in sloth's own small template language — fixed-size blocks, variable-length blocks, string blocks, bitfields, reusable templates, choices, and value functions (`md5`, `sha1`, `crc32`, `size`) that keep checksums and length fields consistent — and every generated file follows that structure with its mutable fields randomized. Point it at a parsing command and it repeatedly generates a file, runs the command on it, and preserves the input whenever the target crashes. This is the tool for formats where fully random input gets rejected at the first header check: magic numbers and framing stay valid while the interior mutates.

## Typical use cases

### Describe a file format in a template

Author the structure the fuzzer will respect.

```
templates {
    segment {
        block<1> = 0xff;
        block<1>;
        block<2> = size(data);
        var_block<0, 2000> data;
    };
};

str_block = "\xff\xd8\xff\xe0";
block<2> = 16;
str_block = "JFIF\x00\x01\x02";
block<8> = 0;
template<segment, 0, 200>;
```

Outcome: a valid template file (say `myformat.tpl`): magic numbers and fixed markers stay constant, unnamed blocks randomize their contents, `var_block` fields randomize their length within bounds, and derived fields (lengths, checksums) recompute correctly — every generated file parses as structurally plausible while its interior mutates.

### Fuzz a parser with a template

Run the generate-execute-watch loop against a target.

```
sloth -t templates/mp3 mpg123
```

Outcome: sloth repeatedly builds a file from `templates/mp3`, appends it to the command (`mpg123 <file>`), and monitors the exit status; each input that crashes the target is moved to `/tmp/fuzzer-N` with N incrementing per crash, so every finding is preserved as a reproducible file. Target arguments pass through (e.g. `mpg123 -2`).

### Control the generated file's name and position in the command

Fuzz targets that demand a specific extension or argument order.

```
sloth -t templates/jpg -o /dev/shm/sloth.jpg convert {%} /dev/shm/sloth.png
```

Outcome: `-o` writes each test case to `/dev/shm/sloth.jpg` instead of the default `/tmp/sloth-file` — required when the target validates the file extension — and `{%}` marks where in the command line the filename is inserted (it is appended at the end when omitted); here ImageMagick's `convert` is fuzzed with a fixed output argument.

### Keep crash artifacts in a persistent location

Make findings survive reboots and long campaigns.

```
sloth -t templates/mp3 -d /home/user/crashes/fuzzer- mpg123
```

Outcome: crashing inputs are stored under `/home/user/crashes/fuzzer-N` instead of `/tmp/fuzzer-N`, so the reproduction corpus persists across reboots for later minimization and debugging.

*No corpus evidence — derived from upstream documentation.*

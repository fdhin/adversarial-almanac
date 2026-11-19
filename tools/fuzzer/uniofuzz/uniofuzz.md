---
name: "uniofuzz"
categories:
  - blackarch-fuzzer
version: "1337"
homepage: "http://nullsecurity.net/tools/fuzzer.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# uniofuzz

> Nullsecurity's universal random-input generator for fuzzing browsers, web daemons, files, programs and network services.

## What it's for

UniOFuzz (pigtail23 / nullsecurity team, v0.1.2-beta) is a single Python 2 script that generates large volumes of random or constant-character strings and throws them at whatever surface you point it at: it writes mutated files for browser and file fuzzing, sprays a web daemon or an arbitrary network port with random data, or pipes strings into a local program. Reach for it when you want dumb, high-volume, template-based fuzzing with no protocol awareness; it performs no monitoring or crash analysis of its own, so pair it with a debugger, sanitizer or attentive log-watching to judge the results.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz a browser with mutated template files

Generate mutated copies of an HTML template for serving to a browser.

```
./uniofuzz.py -b template.html -m 1000 -e html/php/aspx -o /var/www -c A -s 10000
```

Outcome: 1000 mutated template files (constant character `A`, string lengths up to 10000) are written below the output folder with the requested file extensions, ready to be served and loaded into a browser under a debugger.

### Fuzz a web daemon or network service

Spray random strings at a TCP port at a fixed interval.

```
./uniofuzz.py -w -i 0.01 -ip 127.0.0.1 -port 80
```

Outcome: connects to 127.0.0.1:80 and sends a fresh random string every 0.01 s; the daemon's error log (or an attached debugger) shows whether malformed input triggers a crash. Use `-n` instead of `-w` for any other TCP service, for example an SMTP daemon on port 25.

### Fuzz file parsers with generated files

Create files of random content with parser-relevant extensions.

```
./uniofuzz.py -f -m 1000 -e m3u/pdf -o ./files
```

Outcome: 1000 random-content files with `.m3u` and `.pdf` extensions appear in `./files` for feeding a media player or document parser; with `-ft template.m3u` the mutations derive from a seed template instead of pure randomness.

### Pipe strings into a program

Fuzz a program's standard input directly.

```
./uniofuzz.py -p -m 1000 -i 1.5 -d '/bin/ls -n'
```

Outcome: executes `/bin/ls -n` repeatedly (1000 runs, 1.5 s apart), each run receiving a random string on stdin — watch the exit status or attach a debugger to catch parser failures.

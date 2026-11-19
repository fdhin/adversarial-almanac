---
name: "sfuzz"
categories:
  - blackarch-fuzzer
version: "201.9c01dab"
homepage: "http://aconole.brad-x.com/programs/sfuzz.html"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-09-28"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "orgcandman/Simple-Fuzzer"
github-stars: 66
github-pushed: "2026-09-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# sfuzz

> Simple Fuzzer: a scriptable black-box fuzzer that substitutes literals and expandable sequences into test templates and sends them over TCP or UDP.

## What it's for

SFUZZ, "simple fuzz" (Aaron Conole), is a lightweight black-box fuzzer built around small script files. A config defines fuzzing material — `literal=` strings sent as-is and `sequence=` strings that grow incrementally up to `maxseqlen` — plus test templates in which the keyword `FUZZ` marks the substitution point; the engine then produces one copy of each template per fuzz string and either prints them (output mode) or ships them to a TCP/UDP service. The script language also supports variables (`$X=y`), binary substitution (`!X=deadbeef`), previous-packet data references (`|X=[off:len:def]`), and request pacing (`reqwait`). It was written so QA and security staff can hammer a protocol without writing C or learning SPIKE. It is dumb-delivery by design: no coverage feedback and no crash detection — watch the target process and traffic yourself.

## Typical use cases

### Preview generated payloads in output mode

See exactly what would be sent before touching the network.

```
cat > /tmp/basic.cfg <<'EOF'
literal=../../../../etc/
sequence=%n
sequence=A
maxseqlen=1024
endcfg
GET /FUZZ HTTP/1.0

--
EOF
sfuzz -O -f /tmp/basic.cfg
```

Outcome: `-O` output mode prints every test case — each literal, plus each sequence expanded up to `maxseqlen`, substituted for `FUZZ` in the template after the `endcfg` block — so payloads can be sanity-checked or saved as a corpus. The config file must end with a newline.

### Fuzz a TCP service

Deliver the test cases to a listening service.

```
sfuzz -T -f /tmp/basic.cfg -S target.example.com -p 8080 -t 200
```

Outcome: `-T` TCP mode connects to `target.example.com:8080` and sends each test case, waiting up to 200 milliseconds (`-t` takes a millisecond count, minimum 100) for a response and echoing any reply received; run a sniffer alongside and watch the target process for crashes.

### Fuzz a UDP service with hex logging

Binary-friendly delivery over datagrams.

```
sfuzz -U -f /tmp/basic.cfg -S 198.51.100.10 -p 69 -r -X -L sfuzz.log
```

Outcome: `-U` UDP mode sends each payload as a datagram to port 69 with no trailing newline (`-r`, generally wanted for binary protocols), while `-X` logs traffic as a hex dump and `-L` writes it to `sfuzz.log` instead of stdout.

### Resume after a crash and stop on silence

Recover the campaign position when the target dies.

```
sfuzz -T -f /tmp/basic.cfg -S target.example.com -p 8080 -e -b 500
```

Outcome: `-b 500` resumes at test case 500 (the count printed during the previous run), and `-e` halts the campaign as soon as the host stops responding — a crude liveness check that leaves the last payload identifiable instead of streaming into a dead service.

*No corpus evidence — derived from upstream documentation.*

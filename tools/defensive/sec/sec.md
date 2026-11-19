---
name: "sec"
categories:
  - defensive
version: "2.9.0"
homepage: "https://simple-evcorr.github.io/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur, nixpkgs]
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "simple-evcorr/sec"
github-stars: 185
github-pushed: "2026-02-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# sec

> Lightweight Perl event correlator that watches log streams and fires actions when event patterns line up.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SEC (Simple Event Correlator) is a single-process Perl tool for event correlation. It reads event lines from files, named pipes or standard input, matches them against patterns (regular expressions, Perl subroutines) and applies the rules from its configuration files: when a rule's expected event group is observed inside its time window, SEC executes an action — a shell command, an e-mail, a write to another pipe, or creation of a context that other rules key on. A classic example from its manpage is "three SSH login failures for the same user within 1m" triggering a mail alert. It is used for log monitoring, network and security management, and fraud detection. It is deliberately lightweight — no storage, collection layer or dashboards of its own — and complements rather than replaces a SIEM. It can run as a daemon, as a shell-pipeline stage, or interactively in a terminal.

## Typical use cases

### Correlate events from a live log file

Watch a service log and react to event patterns in it.

```
sec --conf=/etc/sec/sshd.conf --input=/var/log/auth.log
```

Outcome: SEC reads the input from its current position, follows new lines as they are written, and fires the rule actions (shell commands, notifications) whenever a correlation window completes. Add `--tail` to jump to the end of the file and only process lines that arrive from now on.

### Correlate a pipeline on standard input

Feed SEC from any producing command without touching files.

```
tail -f /var/log/secure | sec --conf=linux.conf --input=-
```

Outcome: SEC accepts `-` as a stdin input source, applies the rules to each line of the stream and executes matching actions; the pipeline can be composed with journalctl, docker logs or anything else that emits lines.

### Run as a monitoring daemon

Keep correlation running permanently like any other system service.

```
sec --conf=/etc/sec/*.conf --input=/var/log/messages --input=/var/log/secure \
    --syslog=daemon --pid=/var/run/sec.pid --detach
```

Outcome: SEC forks to the background, writes its PID file, logs its own operational messages to syslog facility daemon, and keeps processing both input files until stopped; SIGHUP makes it reopen inputs and re-read rule files.

### Check rule files before deploying

Validate a ruleset without running it.

```
sec --testonly --conf=/etc/sec/main.conf; echo "exit=$?"
```

Outcome: SEC parses the configuration and exits immediately — exit code 0 means no faulty rules, 1 means at least one rule failed to load, so this fits naturally into CI or a deploy script.

### Debug a ruleset interactively

See how rules match while developing them.

```
sec --conf=test.conf --input=- --debug=3
```

Outcome: SEC prints verbose trace of rule matching, contexts and action execution to the terminal while you type or paste sample event lines, making rule timing windows observable.

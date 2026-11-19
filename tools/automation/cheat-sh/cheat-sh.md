---
name: "cheat-sh"
categories:
  - blackarch-automation
version: "6"
homepage: "https://cheat.sh"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-06"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cheat-sh

> Instant cheat sheets for commands and languages, straight from the terminal via curl.

## What it's for

cheat.sh is a unified lookup service over the best community-driven cheat-sheet repositories: it covers 56 programming languages, several DBMSes and 1000+ UNIX/Linux commands, and answers free-text questions with short, copy-pasteable snippets (curated sheets where they exist, on-the-fly answers built from StackOverflow where they don't). The zero-install interface is plain `curl https://cheat.sh/<topic>`; the optional `cht.sh` client adds an interactive shell mode with query history, per-language context, clipboard integration and tab completion. It is a mid-task lookup aid — concise answers in under ~100 ms — not a documentation browser or offline reference, though the stack can be self-hosted with Docker.

## Typical use cases

### Look up one command

The one-liner form, usable anywhere curl exists.

```
curl cheat.sh/tar
```

Outcome: prints a compact cheat sheet for `tar` to the terminal — common usages only, no manual-page noise.

### Find a command when you only know the task

Search by keyword when the command name is unknown.

```
curl cheat.sh/~snapshot
```

Outcome: returns the cheat sheets and commands matching the keyword "snapshot", letting you pick the right tool by name.

### Ask a programming-language question

Free-text questions under a language namespace, `+` for spaces.

```
curl 'cht.sh/python/append+file?Q'
```

Outcome: prints a code-only answer for the question (the `?Q` option strips explanatory comments); append `/1`, `/2` to the query to request alternative answers, or `?T` to also disable syntax-highlighting ANSI codes.

### List what exists for a language

Discover the available sheets before querying blindly.

```
curl cheat.sh/go/:list
```

Outcome: prints every cheat-sheet name in the `go` namespace; the special page `:learn` (e.g. `cheat.sh/go/:learn`) gives a language-basics intro derived from the "Learn X in Y Minutes" project.

### Work interactively with the cht.sh client

Stay in one shell for many queries.

```
cht.sh --shell
```

Outcome: opens the cheat shell prompt; `cd go` pins the language context so subsequent queries need only the question, `c` (or `C`) copies the last answer — without comments — to the clipboard, and `help` lists the remaining built-ins.

*No corpus evidence — derived from upstream documentation.*

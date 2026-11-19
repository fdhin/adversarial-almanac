---
name: "jiraffe-install"
categories:
  - webapp
---

# Installing jiraffe

Python package on PyPI — the recommended install:

```bash
pip install jiraffe
python3 -m jiraffe --help
```

From source:

```bash
git clone https://github.com/0x48piraj/jiraffe.git
cd jiraffe
python3 -m pip install .
```

Run as a module (`python3 -m jiraffe`) — that is the documented entry point. Unit tests, if hacking on it: `python3 -m unittest --verbose` from the repo root.

Verification: `python3 -m jiraffe --list-exploits` prints the module catalogue.

---
name: "redelk-install"
categories:
  - automation
---

# Installing redelk

Server install, as part of the red team infra deployment:

```sh
tar xzf elkserver.tgz          # extract the release package on the RedELK server
./install-elkserver.sh dryrun  # recommended first pass: checks + .env only
./install-elkserver.sh         # full stack; add 'limited' to skip Neo4j + Jupyter
```

Installer parameters: `dryrun`, `fixedmemory` (1GB heaps for elasticsearch/neo4j), `limited` (no Neo4j/Jupyter), `dev`. After install: define C2 servers in `mounts/redelk-config/etc/crond.d/redelk`, log in to the HTTP interface with the credentials from `redelk_passwords.cfg`, then configure alarms in `mounts/redelk-config/etc/redelk/config.json` (`.env` holds the docker settings).

The manual path — naming requirements, key/package generation, redirector and C2-server client installs — is documented step-by-step in the GitHub wiki. Community Ansible playbooks: server (fastlorenzo/redelk-server), client (fastlorenzo/redelk-client), combined (curi0usJack/ansible-redelk).

Verification: `docker logs redelk-logstash` shows connections established and no parse errors once clients report in.

Backlinks: [[automation]] · [[redelk]]

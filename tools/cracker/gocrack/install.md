---
name: "gocrack-install"
categories:
  - cracker
---

# Installing gocrack

Workers need hashcat 6.x+ built as a **shared** library and an OpenCL-capable GPU stack (Linux — Ubuntu 16.04+ — or macOS; NVIDIA or AMD GPUs).

1. Build hashcat in shared mode: set the `SHARED` bit to 1 in its `src/Makefile` (or apply the patch in the repo's `docker/files/hashcat_shared.patch`), compile it per hashcat's BUILD.md, then expose its headers:

```sh
cp -r hashcat/include/ /usr/local/include/hashcat
hashcat --opencl-info   # should list your OpenCL devices
```

2. Build the GoCrack server and workers (on Linux install libOpenCL plus your platform's ICDs first; `clinfo` should list your devices):

```sh
make build    # or: make SERVBUILDTAGS="auth_database auth_ldap"
```

Docker containers for building and running gocrack live in the repo's `docker/` tree; full build docs in `docs/building.md`, administrator and user guides under `docs/`.

Verification: start the server, open the web UI, and log in — with the database plugin the initial `admin` credentials are documented in `docs/administrator/first_run.md` and must be changed on first login.

Backlinks: [[cracker]] · [[gocrack]]

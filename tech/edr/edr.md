---
technology: EDR/AV telemetry surface (detection engineering for operators)
kind: overview
audience: offensive-security
status: upstream-only
family: endpoint-security
layer: endpoint agent telemetry (user-mode hooks, kernel callbacks, ETW, cloud analytics)
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave 2026-11-13; telemetry mechanics verified against Microsoft's primary documentation during the wave: ETW architecture (kernel-level tracing, controller/provider/consumer model, dynamic session enablement), AMSI integration surface (UAC elevation, PowerShell, Windows Script Host, JavaScript/VBScript, Office VBA), and kernel process-notification callbacks (PsSetCreateProcessNotifyRoutine semantics: callback after initial-thread creation, up to 64 registered process-creation callbacks). Carries zero CVEs by design — the unit documents a telemetry model, not version-gated bugs"
source: "six2dez/pentest-book (others/rt-edr.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# EDR/AV telemetry surface

> An EDR is a telemetry pipeline wearing a product logo: hooks in your process, callbacks in the kernel, and a cloud scorer reading the exhaust — the operator's job is not "bypassing" it, it is knowing exactly which sensor produces which signal so nothing you do is newsworthy.

This page is an offensive *reference*: it documents what endpoint security products instrument and why standard offsec technique lights up which sensor. Read it as detection engineering from the attacking side — knowing the telemetry model is what tells you what **not** to do on an engagement. It names technique families and their detection consequences; it is not a bypass cookbook, and the honest conclusion it builds toward is that most "EDR bypass" content is really *detection-deficit arbitrage* that closes the moment a vendor writes a rule.

## What it is

"EDR/AV" spans three generations of the same idea: antivirus (static, file-hash and signature oriented), next-gen AV (behavioral and reputation scoring, on-write emulation), and EDR (full telemetry collection with centralized correlation). A modern agent is architecturally consistent across vendors: a **user-mode sensor** inside or adjacent to interesting processes, a **kernel-mode driver** for file system and process telemetry, a **local ingestion/correlation engine**, and a **cloud pipeline** where the actual scoring happens. Nothing is detected "on the endpoint" in the marketing sense — events are collected on the endpoint and *judged* somewhere faster and better informed.

The operator takeaway: you never face "the EDR" as one thing. You face each sensor's specific signal, plus the analytics' tolerance for your baseline. A technique that beats the hook in your process but emits a kernel event is not evading anything — it is re-serializing the same alert.

## The anatomy: what actually gets instrumented

    user mode
      injected hook DLL → inline patches on ntdll exports
        ( Zw*/Nt* syscall stubs ) → every API call the product cares about
      AMSI content scans → script content BEFORE execution
      .NET instrumentation → assembly loads, in-memory sources
    kernel mode
      process-creation callbacks → every spawn: image path, parent, cmdline
      image-load callbacks → every module mapped
      file-system minifilters → every create/read/write
      registry/object callbacks → persistence surfaces, handle ops
    telemetry fabric
      ETW → providers emit; controllers enable sessions; consumers read
        ( kernel + Microsoft-Windows-DotNETRuntime + security logs )
    cloud
      reputation, sandboxed detonation, behavioral correlation across fleet

### User-mode hooks

The classic EDR mechanism: a DLL injected into (nearly) every process patches the front of `ntdll`'s syscall stubs, so each call transits vendor code before reaching the kernel. What it sees: process/thread/memory operations, handle duplication — the entire `OpenProcess`→`VirtualAllocEx`→`WriteProcessMemory`→`CreateRemoteThread` choreography. What it costs the defender: the hook itself is user-mode code in a hostile process, which is why the entire unhooking/syscall family of technique exists (below).

### AMSI

The Antimalware Scan Interface is a Windows-provided channel (not a vendor invention) that hands script *content* to registered antimalware providers before execution. Microsoft's integration list is the exact attack surface of the bypass family: **UAC elevation (EXE/COM/MSI/ActiveX installs), PowerShell (scripts, interactive use, dynamic code evaluation), Windows Script Host (wscript/cscript), JavaScript and VBScript, and Office VBA macros**. The consequence for operators: obfuscating a PowerShell payload does not help if the deobfuscated content is scanned at invocation — the content arrives at the sensor regardless of how it was encoded in transit.

### ETW

Event Tracing for Windows is the telemetry fabric itself: a kernel-level tracing facility with a controller/provider/consumer architecture — controllers start sessions and enable providers, providers emit events, consumers (the EDR agent, Sysmon, Defender's own pipeline) read the streams in real time or from log files. Microsoft documents dynamic enablement as a core property: tracing turns on and off without restarting the traced processes — you cannot age an ETW sensor out of memory. Providers of operational interest: the kernel process/image/thread providers, `Microsoft-Windows-DotNETRuntime` (assembly loads — the bane of reflective .NET tradecraft), PowerShell's script-block logging (event ID 4104 records deobfuscated script content — the AMSI corollary in log form), and the CodeIntegrity channels for WDAC events.

### Kernel callbacks

The kernel side cannot be patched from user mode and does not care how you made your syscalls. `PsSetCreateProcessNotifyRoutine` registers a driver callback invoked for every process creation and exit — documented to fire just after the initial thread is created in the new process, carrying parent and child PIDs — and Windows allows up to 64 such registered process-creation callbacks, a list every major agent occupies. Siblings cover image loads, thread creation, and object/handle operations (Ob callbacks gate `OpenProcess`-style access to protected processes); minifilters sit in the file-system stack and see every file operation, including the pattern-of-life signals (write-then-execute) that detonation logic keys on.

### Cloud scoring

On-access scanning (on-write or on-execute, with emulation for packers), file and certificate reputation, and fleet-wide behavioral correlation: the same binary appearing on N endpoints in M minutes, the same crash pattern, the same C2 beacon timing. This layer explains detections with no on-box explanation — the artifact that was individually innocent and collectively damning.

## What each sensor implies

The detection matrix, sensor → signal → why the classic technique is loud:

| Sensor | Signal | Classic technique it buries |
|---|---|---|
| ntdll hooks | API call sequences | textbook injection chains (`CreateRemoteThread` et al.) |
| AMSI | script content pre-execution | obfuscated PowerShell/VBS/JScript droppers |
| ETW (.NET provider) | assembly names, loads | execute-assembly of .NET tooling, reflective loads |
| ETW (PowerShell 4104) | deobfuscated scripts | anything that "hid" its logic from the command line |
| process callbacks | parent/child + cmdline | Office-spawns-cmd, LOLBin parent/child anomalies |
| image-load callbacks | module mappings | reflective DLL imports, unsigned drivers |
| minifilter | write→execute patterns | dropped EXEs/DLLs, double-extension tricks |
| cloud correlation | fleet-wide patterns | re-used payloads across endpoints |

Read the table operationally: every row is a thing you stop doing, not a thing you defeat. Parent/child anomalies are the cheapest wins for defenders — a document reader spawning a shell is a sentence no hook needs to parse.

## Evasion technique families, honestly

Each family below is named with *what it defeats* and *what still sees it* — the part most bypass marketing omits:

- **AMSI patching** (setting the `amsiInitFailed` reflection field; in-memory patching of `AmsiScanBuffer` to return clean). Defeats content inspection for the patched process. Still seen by: the patch act itself (memory-diff scans), ETW, and any sibling process where AMSI is intact. Heavily signatured in its public forms.
- **ETW patching** (patching `EtwEventWrite` to return immediately). Blinds the *user-mode* provider path in that process. Still seen by: kernel callbacks, other processes' providers, and the absence-of-events anomaly itself.
- **Unhooking / direct syscalls** (restoring clean ntdll from disk; SysWhispers-style stub generation; Hell's Gate dynamic syscall-number resolution — reading the syscall number out of ntdll at runtime — and Halo's Gate, deriving a hooked stub's number from its unhooked neighbors). Defeats user-mode hooks *only*. Kernel callbacks receive the same syscall regardless of how it was issued — the telemetry moves, it does not disappear.
- **Injection taxonomy** (classic remote-thread; process hollowing — suspended process, unmapped image, payload written, resumed; thread hijacking — repointing an existing thread's instruction pointer; APC queueing, including early-bird APCs before the target's main runs; module stomping — executing payload from inside a legitimately loaded module's memory). Each variant was designed against a specific detector (unbacked-memory scans, thread-creation events), and each newer variant trades one signal for another — hollowing still emits process-creation and image-load telemetry; stomping still leaves module-modification evidence.
- **Phantom-file techniques** (process ghosting — create file, mark delete-pending, image the section, let the file vanish, execute the section; Doppelgänging via NTFS transactions; Herpaderping — replacing the on-disk copy after the image is mapped). All three attack the *on-disk scan window*: the file the AV would scan is not the file that executes. Still seen by: kernel image callbacks (the image is mapped either way), memory scans, and cloud detonation of the dropped artifact whenever anything landable reaches disk.
- **LOLBAS abuse** (MSBuild inline tasks, InstallUtil, regsvr32 scriptlets, mshta, rundll32-with-JS, certutil/bitsadmin fetching). Not evasion at all — *policy exploitation*: the binaries are signed and trusted. Seen by: process telemetry (the parent/child and command-line sentences), script-content logging where AMSI sits in the interpreter, and WDAC/AppLocker policy itself.
- **Obfuscation, encryption, signing** (string XORing, AES-wrapped payloads, loaders from donut/ScareCrow/PEzor-class tooling, stolen or purchased code-signing identities). Changes the *static* appearance of artifacts. Cloud reputation keys heavily on certificates and artifact similarity — re-used loaders and leaked signing certs both burn, and a burned cert's entire artifact family retroactively becomes indicator material.

The pattern across every family: techniques divide into *defeat one sensor* (hooks, AMSI, a scan window) and *change your behavior so no sensor fires* (in-process execution without suspicious API shapes, living in allowed hosts, plausible parent/child chains). The second category is tradecraft; the first is a race you win per-host and lose per-rule.

## Why offsec tooling gets flagged — the tool-detection surface

Public tooling is detected less by magic than by artifact economics: vendors collect the artifacts because everyone runs the same tools. The surface:

- **Static signatures**: mimikatz byte patterns, Cobalt Strike's default beacon behavior and Malleable-C2 defaults, Metasploit stager conventions — matched on disk and in memory scans, including on-write emulation.
- **Default operational artifacts**: named pipes and sleep-mask behaviors, known inject patterns, hardcoded strings and PDB paths in compiled tools, the byte-shape of public loader generators (a donut or ScareCrow build is a *class* the classifier knows).
- **Execution-shape anomalies**: execute-assembly producing a .NET assembly named nothing the fleet ever names; PowerShell with unusual host combinations; debuggers and instrumentation artifacts.
- **Source-signing**: tools signed with expired/revoked/stolen certificates, or unsigned drivers — kernel callback policy plus WDAC both key on it.

Engagement consequence: assume default configuration of any public tool is a detection event on a mature fleet. The reference-level countermeasure set is boring on purpose — rebuild/rename/re-sign, prefer in-memory and in-process execution over injection into unrelated hosts, keep parent/child chains plausible, stage as little on disk as the rules of engagement allow, and never reuse one payload artifact across N endpoints of the same tenant (that is the cloud-correlation row of the matrix).

## Enumerating what is actually installed

Before any of the above matters, inventory the endpoint's sensors — this is reconnaissance, and it is quiet:

```powershell
Get-Process | Where-Object {$_.ProcessName -match "MsMpEng|CrowdStrike|Carbon|Cylance|Tanium|SentinelOne"}
Get-Service | Where-Object {$_.DisplayName -match "CrowdStrike|Carbon Black|Cylance|SentinelOne|Tanium|Defender"}
Get-Process -Id $PID | Select-Object -ExpandProperty Modules | Select-Object FileName   # injected hook DLLs
Get-WinEvent -ListProvider *Defender*,*Sysmon* -ErrorAction SilentlyContinue           # telemetry channels present
```

Kernel-side (which callbacks and filters are registered) requires your own driver or existing tooling (CheekyBlinder-class callback removers exist) — on most engagements enumerating user-mode presence plus the event channels is sufficient to choose the tradecraft tier.

## Honest limits of this unit

The kernel tier cannot be silenced from user mode — everything under "kernel callbacks" is *visible regardless* of user-mode evasion, and techniques that attack the sensor itself (callback removal, driver kill) are tamper events with their own alert class. BYOVD (bring-your-own-vulnerable-driver) exists and is heavily tracked; on a mature deployment it converts an evasion problem into an IR timeline. The durable operator skill this unit encodes is the matrix above: which behaviors are cheap for defenders to see, and planning engagements that never require the expensive ones.

## Where to go next

- [[edr-bug-bounty]] — the phased method for auditing an endpoint's telemetry posture

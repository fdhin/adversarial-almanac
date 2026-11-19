---
technology: EDR/AV telemetry surface (detection engineering for operators)
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: endpoint-security
version: "1.0"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave 2026-11-13; audit method synthesized from the upstream reference's evasion content inverted into a detection-engineering checklist; sensor facts verified against Microsoft documentation (ETW controller/provider/consumer, AMSI integration list, PsSetCreateProcessNotifyRoutine callback semantics and the 64-callback cap). Zero CVEs by design"
source: "six2dez/pentest-book (others/rt-edr.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# EDR telemetry audit methodology

Companion overview: [[edr]].

Scope: auditing an endpoint's detection posture — what sensors are present, what events they actually emit, and how planned tradecraft maps onto that telemetry. This is the method file for operators who need to know *what not to do*; it is not a bypass procedure. Every phase produces a go/no-go input, not an exploit.

## Phase 0 — Inventory the sensor stack

Quiet enumeration only; nothing in this phase should itself be detectable as evasion reconnaissance.

```powershell
# Agent processes and services (extend the pattern per vendor)
Get-Process | Where-Object {$_.ProcessName -match "MsMpEng|CrowdStrike|Carbon|Cylance|Tanium|SentinelOne|cb"}
Get-Service | Where-Object {$_.DisplayName -match "CrowdStrike|Carbon Black|Cylance|SentinelOne|Tanium|Defender"}

# Hook DLLs present in your own process
Get-Process -Id $PID | Select-Object -ExpandProperty Modules | Select-Object FileName

# Telemetry channels that exist on this host
Get-WinEvent -ListProvider *Defender*,*Sysmon*,*PowerShell* -ErrorAction SilentlyContinue
Get-CimInstance -ClassName Win32_DeviceGuard -Namespace root\Microsoft\Windows\DeviceGuard -ErrorAction SilentlyContinue
```

Decision point: the product set found determines everything downstream — Defender-family means AMSI + ETW + cloud reputation are all live; a third-generation EDR adds kernel callbacks you cannot enumerate without a driver. WDAC/DeviceGuard output decides whether unsigned-driver tradecraft is even loadable (audit vs enforced mode matters; check CodeIntegrity event channel 3076/3077 for audit-mode evidence).

## Phase 1 — Baseline the telemetry that actually flows

On a system you are authorized to test, generate *benign* canary events and observe which channels record them:

```powershell
# Spawn a perfectly ordinary process chain, then read what the sensors said
notepad.exe; Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 5 -ErrorAction SilentlyContinue
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/ScriptBlock" -MaxEvents 5 -ErrorAction SilentlyContinue   # 4104 = content logging on
Get-WinEvent -LogName "Microsoft-Windows-CodeIntegrity/Operational" -MaxEvents 10 -ErrorAction SilentlyContinue
```

Expected outputs decide the tradecraft tier: Sysmon operational log present → process/image/file telemetry is being *consumed* somewhere; 4104 script-block events present → PowerShell content is recorded post-obfuscation (any PowerShell staging is reportable); CodeIntegrity 3076 events → WDAC in audit mode (policy exists but not enforced — a finding, not a wall).

## Phase 2 — Map the hook surface

Characterize the user-mode sensor without attacking it:

```powershell
# Compare in-memory ntdll stubs against the on-disk copy (procedure, not weapon):
#   1. Read ntdll.dll bytes from disk (KnownDlls copy).
#   2. Read the mapped ntdll .text in a sacrificial process.
#   3. Byte-compare exported syscall stubs; differing first bytes = inline hooks.
```

Record which export families are hooked (`NtAllocateVirtualMemory`, `NtWriteVirtualMemory`, `NtCreateThreadEx`…). This is an input to planning, not a bypass: hooked families tell you which API shapes are user-mode-visible, and the kernel-callback row of the [[edr]] matrix still applies to everything.

## Phase 3 — Profile planned tradecraft against the matrix

For each planned action, name the sensor that will see it, using the overview's detection matrix:

- Any `Process.Start`/cross-process work → process-creation callback (kernel; always fires). Make the parent/child sentence plausible or do not do it.
- Any .NET assembly load → DotNETRuntime ETW provider, regardless of how the assembly was delivered.
- Any script interpreter (PowerShell, wscript, mshta, Office macros) → AMSI content scan + likely 4104 logging; obfuscation changes nothing post-invocation.
- Any on-disk artifact → minifilter write events + on-write emulation + cloud reputation; re-using one artifact across endpoints converts it into a correlation signal.
- Any injection into an unrelated host → the host's own hook set; prefer in-process execution over cross-process shaping.

Decision point: actions whose only cover is "defeats one sensor" (phase-1 patching, unhooking alone) are rejected for mature fleets — they trade an alert for a tamper signal. Actions with no kernel event and no content scan are the keepers.

## Phase 4 — Validate on a canary host

Where authorization allows, exercise the *planned* chain (loader shape, execution host, egress timing) on one canary endpoint before fleet-wide use. Diff the event channels from phase 1: any new event class the chain produced is a detection surface to re-plan around. Time-box this — the validation host's baseline is now contaminated; do not reuse it for the operation itself.

## Phase 5 — Report anchors

Report what the telemetry model means for the client, not a bypass scorecard: which of their channels were dark (missing Sysmon, script-block logging off, WDAC audit-only), which detections were purely cloud-correlation (invisible to on-host responders), and which policy shapes (AppLocker/WDAC rules, allowed-path bypasses) the audit exercised. The deliverable is their sensor-coverage map; the operator's deliverable is the same map read in reverse.

## Tool mapping

Name tools in the report, use them inside the matrix discipline above: SysWhispers-family generators and Hell's Gate/Halo's Gate resolution for hook-shape research; donut/ScareCrow/PEzor-class loaders as the *test artifacts* for phase 4 (expect them to be signatured — that is the point of testing with known-loud artifacts first); CheekyBlinder-class tooling only where driver-level callback enumeration is explicitly in scope.

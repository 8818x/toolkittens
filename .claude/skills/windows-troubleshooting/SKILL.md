---
name: windows-troubleshooting
description: Diagnose Windows PC problems including crashes, slow performance, networking, drivers, services, Windows Update, application failures and hardware-related symptoms. Use when a Windows computer or installed software is not working correctly.
---

# Windows Troubleshooting

## Rules

1. Gather evidence before modifying the system.
2. Identify Windows version and hardware first.
3. Check logs and error codes.
4. Prefer reversible diagnostic actions.
5. Do not uninstall, reset, delete, format, or modify boot configuration without identifying the consequence first.

## Diagnostic flow

1. Determine the symptom.
2. Reproduce or identify when it occurs.
3. Collect relevant system information.
4. Inspect Event Viewer/logs.
5. Check processes, services, drivers, disk, memory and network as relevant.
6. Form a root-cause hypothesis.
7. Test the hypothesis.
8. Apply the minimum necessary fix.
9. Verify the original problem is resolved.

## Evidence collection (read-only first)

| ข้อมูล | คำสั่ง |
|---|---|
| System info | `Get-ComputerInfo` |
| Processes | `Get-Process` |
| Services | `Get-Service` |
| Disk | `Get-PSDrive` |
| Event errors | `Get-WinEvent -FilterHashtable @{LogName='System';Level=2,3} -MaxEvents 50` |
| DNS | `Resolve-DnsName <host>` |

Tool layer เพิ่มเติม (เมื่อต้องการเจาะลึก): Sysinternals — Process Explorer (process/handles/DLL), Process Monitor (file/registry activity), Autoruns (startup/persistence), TCPView (connections), RAMMap (memory), ProcDump (crash/hang dumps) และ Microsoft TSS สำหรับเก็บ diagnostics ครบชุด

## โครงผลลัพธ์ที่ต้องรายงาน

```
SYMPTOM
↓
OBSERVATIONS
↓
EVIDENCE
↓
HYPOTHESES 1..n
↓
NEXT DISCRIMINATING TEST
↓
ROOT CAUSE
↓
PROPOSED MINIMAL FIX (risk / rollback)
↓
APPLY
↓
VERIFY ORIGINAL SYMPTOM
```

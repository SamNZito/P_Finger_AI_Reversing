# Reverse engineering notes

Living document for P_Finger AI-assisted RE. Tie claims to REA evidence; mark unverified items clearly.

Full EAC/BE notes live in the Development share (`docs/re-notes-eac.md`, `docs/re-notes-be.md`). This file records the **2026-11-18 dynamic lab load** on guest `AI_workspace`.

## Verified

Lab copies only, guest `C:\re\artifacts\`, hashes matched. Not live game, not host Program Files. DSE was not disabled.

| Claim | Evidence |
|-------|----------|
| Guest hostname `AIWORKSPACE`; images hash-match EOS `26797A11…`, legacy `DD60A8CD…`, BEDaisy `E7BCF344…`; Authenticode Valid | `dyn-lab-probe.txt` |
| CI enforcement **2**, VBS **0**, no `testsigning` / `nointegritychecks` / kernel `debug`; WinDbg/kd absent | `dyn-lab-load.txt`, `bcdedit {current}` |
| EOS + legacy: bare `sc start` as `LabDynEAC*` → ERROR 2, FltMgr **0xC0000034** (missing Instances). CI did **not** reject the images | `dyn-lab-load.txt`, System event 5 |
| EOS + Instances: RUNNING; `fltmc` `EasyAntiCheat_EOS` altitude **370010**, 6 instances; `\\.\EasyAntiCheat_EOS` CreateFile **win32=0** | `dyn-lab-minifilter.txt` |
| Legacy + Instances: RUNNING; `fltmc` `EasyAntiCheat` altitude **370020**, 6 instances; `\\.\EasyAntiCheat` CreateFile **win32=0** | `dyn-lab-minifilter.txt` |
| `\\.\EasyAntiCheat_EOSSys` does **not** exist (win32=2) | same |
| `LabDynBEDaisy`: DriverEntry reached `FltRegisterFilter`; FltMgr event 5 **0xC0000034** | System event 5 at 14:53:11 |
| Product-named `sc start BEDaisy`: SCM **577**, CI Operational **3004** ("file hash could not be found") | `dyn-lab-ci-events.txt` |
| `\\.\BattlEye` / `\\.\BEDaisy` never opened (win32=2) | all probes |
| Lab services deleted; no leftovers | `sc query` after cleanup |

## Hypothesis

- EOS IRP `DEVICE_CONTROL` codes still unknown (no WinDbg, no `DeviceIoControl` sent).
- BEDaisy DOS name still H-BE-01 (driver never stayed RUNNING under the product name).

## Addresses

Runtime DOS names only (not kernel `\Device\` object dumps):

- `\\.\EasyAntiCheat_EOS`
- `\\.\EasyAntiCheat`

## Next steps

- Optional: install WinDbg + kernel debug pipe on `AI_workspace` to dump dispatch/IOCTL tables **after** a lab load. Do **not** disable DSE to force BEDaisy.
- Keep using `C:\re\artifacts\` copies only.

Logs on the Development share: `ac-captures-host/2026-11-18/dyn-lab-*.txt`.

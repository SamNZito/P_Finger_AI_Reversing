# Reverse engineering notes

Living document for P_Finger AI-assisted RE. Tie claims to REA evidence; mark unverified items clearly.

Full EAC/BE notes live in the Development share (`docs/re-notes-eac.md`, `docs/re-notes-be.md`). This file records the **2026-11-18 dynamic lab load** and **WinDbg IOCTL capture** on guest `AI_workspace`.

## Verified

Lab copies only, guest `C:\re\artifacts\`, hashes matched. Not live game, not host Program Files. DSE was not disabled. Secure Boot was not disabled.

| Claim | Evidence |
|-------|----------|
| Guest hostname `AIWORKSPACE`; images hash-match EOS `26797A11…`, legacy `DD60A8CD…`, BEDaisy `E7BCF344…`; Authenticode Valid | `dyn-lab-probe.txt` |
| CI enforcement **2**, VBS **0**, no `testsigning` / `nointegritychecks` | `dyn-lab-load.txt`, `bcdedit {current}` |
| EOS + Instances: RUNNING; `fltmc` altitude **370010**, 6 instances; `\\.\EasyAntiCheat_EOS` CreateFile **win32=0** | `dyn-lab-minifilter.txt`, `dyn-lab-ioctl.txt` |
| Legacy + Instances (solo): RUNNING; altitude **370020**, 6 instances; `\\.\EasyAntiCheat` CreateFile **win32=0** | same |
| `\\.\EasyAntiCheat_EOSSys` does **not** exist (win32=2) | same |
| WinDbg **1.2611.4001.0** + SDK Debuggers **10.0.22621.755** (`cdb`/`kd`/`windbg`) installed on the guest | `dyn-lab-windbg-install.txt` |
| `bcdedit /debug on` **Secure Boot protected**; `kd -kl` HRESULT **0x80004001** (“local kernel debugging is disabled”) | `dyn-lab-windbg-install.txt`, `dyn-lab-ioctl-kd.txt` |
| Empty `NtDeviceIoControlFile` on `\\.\EasyAntiCheat_EOS` and `\\.\EasyAntiCheat` for `0x0` / `0x00220000` / `0x00220003` → **NTSTATUS 0**, 0 bytes (MJ `DEVICE_CONTROL` present; not a CTL_CODE catalog) | `dyn-lab-ioctl.txt`, `dyn-lab-ioctl.json` |
| `cdb` `LabIoctlProbe.exe \\.\EasyAntiCheat_EOS`: `dio_ok=True win32=0 ret=0` | `dyn-lab-ioctl-cdb.txt` |
| Product-named `sc start BEDaisy`: SCM **577** (reconfirmed). `\\.\BattlEye` / `\\.\BEDaisy` win32=2 | `dyn-lab-ioctl.txt`, `dyn-lab-ci-events.txt` |
| Lab services deleted; no leftovers | `sc query` after cleanup |

## Hypothesis

- Empty-buffer NTSTATUS 0 means the dispatch no-ops unknown codes; it does **not** identify live IOCTL function numbers.
- BEDaisy DOS name still H-BE-01 (product-named start never leaves CI 577). Handler VA needs local kd, which Secure Boot refuses.

## Addresses

Runtime DOS names only (not kernel `\Device\` object dumps — `kd -kl` blocked):

- `\\.\EasyAntiCheat_EOS`
- `\\.\EasyAntiCheat`

## Next steps

- Local kd / `!drvobj` needs Secure Boot policy that allows `bcdedit /debug on`. Do **not** disable DSE to force BEDaisy.
- Keep using `C:\re\artifacts\` copies only.

Logs on the Development share: `ac-captures-host/2026-11-18/dyn-lab-*.txt`.

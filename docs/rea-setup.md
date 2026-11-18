# REA setup (Cursor + P_Finger_AI_Reversing)

Recorded **Nov 17, 2026** (Eastern, UTC-5) on Sam's Windows PC.

## Versions

| Component | Version / path |
|-----------|----------------|
| Node.js | **22.23.5** (`C:\Program Files\nodejs\node.exe`) — upgraded via `winget upgrade --exact --id OpenJS.NodeJS.22` |
| npm | **10.9.2** |
| REA (`rea-agents`) | **6.9.0** |
| REA skill | **62** → `C:\Users\bj284\.agents\skills\reverse-engineer-anything` |
| Java (REA env) | `JAVA_HOME=C:\Program Files\Microsoft\jdk-21.0.13.21-hotspot\` |
| Ghidra | **Not installed** (`GHIDRA_INSTALL_DIR` unset) |

**Note:** Cursor's cloud/shell agent sometimes reports a bundled Node (e.g. 24.x). REA and MCP must use **system** Node at `C:\Program Files\nodejs`, which is what setup wrote into Cursor's MCP config.

## Commands run

```powershell
# Version check (use system Node, not Cursor-bundled node)
& "C:\Program Files\nodejs\node.exe" -v
& "C:\Program Files\nodejs\npm.cmd" -v

# Node upgrade (admin UAC prompt)
winget upgrade --exact --id OpenJS.NodeJS.22 --accept-package-agreements --accept-source-agreements

# REA → Cursor (non-interactive; equivalent to choosing Cursor in the setup TUI)
cd c:\Users\bj284\Downloads\TraeTesting\cursor\Reversing
& "C:\Program Files\nodejs\npm.cmd" exec rea-agents@latest -- setup --yes --client cursor

# Health checks
& "C:\Program Files\nodejs\npm.cmd" exec rea-agents@latest -- doctor --client cursor --format json
```

## Config files changed

| File | Change |
|------|--------|
| `C:\Users\bj284\.cursor\mcp.json` | **Created** — registers MCP server `rea` (stdio via `rea.mjs mcp`) |
| `C:\Users\bj284\.agents\skills\reverse-engineer-anything` | **Installed/updated** by REA setup |

This repo also has a project skill at `.cursor/skills/rea-ai-reversing/SKILL.md` (project-specific notes).

### Cursor MCP registration (summary)

Server name: **`rea`**. Command: `node.exe` + cached `rea-agents` `rea.mjs` + `mcp`. Env includes `JAVA_HOME` as detected during setup.

Re-run setup after upgrading REA or clearing npm's npx cache:

```powershell
& "C:\Program Files\nodejs\npm.cmd" exec rea-agents@latest -- setup --yes --client cursor
```

## Setup result

| Check | Status |
|-------|--------|
| `rea setup --client cursor` | **Success** (`configured_cursor`, `installed_skill`) |
| `rea doctor --client cursor` | **healthy: true** (registration aligned) |
| MCP process smoke test | Starts without immediate exit |
| Analysis backend (Ghidra) | **Missing** — MCP works; binary analysis needs Ghidra |

## What Sam must do in Cursor (UI)

1. **Fully quit Cursor** (all windows), then reopen — or **Developer: Reload Window**.
2. Open **Cursor Settings → MCP** (or Features → MCP). Confirm **`rea`** appears and toggles **on** (green).
3. On first agent use, **approve** MCP / tool access if Cursor prompts.
4. Open this repo (`P_Finger_AI_Reversing`) and start a chat; REA tools should appear when the agent can call MCP (e.g. ~255 tools per doctor catalog).

**Verify in chat:** Ask the agent to run `rea doctor --client cursor` or any REA-backed analysis once Ghidra is configured.

## Ghidra (manual — not installed)

No Ghidra package was found in `winget search ghidra`. Install manually:

1. Install **JDK 21+** (you already have Microsoft JDK 21; Ghidra's site currently documents JDK 25 for latest releases — either is fine if Ghidra version matches its readme).
2. Download the latest **Assets** zip (not "Source code") from [Ghidra releases](https://github.com/NationalSecurityAgency/ghidra/releases) (`ghidra_*_PUBLIC_*.zip`).
3. Extract to a fixed path, e.g. `C:\Tools\ghidra_12.x_PUBLIC`.
4. Point REA at it — add to `C:\Users\bj284\.cursor\mcp.json` under `mcpServers.rea.env`:

   ```json
   "GHIDRA_INSTALL_DIR": "C:\\Tools\\ghidra_12.x_PUBLIC"
   ```

5. Restart Cursor again. Run:

   ```powershell
   & "C:\Program Files\nodejs\npm.cmd" exec rea-agents@latest -- doctor --format json
   ```

   until the `ghidra` check is **ok**.

6. Optional GUI: run `ghidraRun.bat` inside the extract folder.

## Git

This checkout (`c:\Users\bj284\Downloads\TraeTesting\cursor\Reversing`) has **no `.git` directory** and `git` is not on PATH in the agent shell — **no commit/push** was performed. Add this file to your GitHub repo when git is available.

## Troubleshooting

- **`REA requires Node.js 22.x (>=22.23.4)...`** — Refresh PATH or open a new terminal; confirm `node -v` is 22.23.5 from `C:\Program Files\nodejs`.
- **PowerShell blocks `npx`** — Use `npx.cmd` or `npm.cmd exec rea-agents@latest -- …`.
- **MCP missing after npm cache clear** — Re-run `setup --yes --client cursor` (npx cache path in `mcp.json` may change).
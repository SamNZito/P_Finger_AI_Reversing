---
name: rea-ai-reversing
description: >-
  Use REA (Reverse Engineer Anything) MCP with a coding agent to investigate
  native binaries, document findings, and implement mods or research builds.
  Use for P_Finger_AI_Reversing, game RE, Ghidra/IDA workflows, and when the
  user references morluto/rea or rea.today.
environments:
  - local
---

# REA + agent reversing

Ground truth: [rea.today/get-started](https://rea.today/get-started), [morluto/rea](https://github.com/morluto/rea).

Sam's project repo: `https://github.com/SamNZito/P_Finger_AI_Reversing`. Run on the user's PC, not cloud VMs, unless they ask otherwise.

## When to use

- Investigating an unfamiliar PE/native binary (game client, anti-cheat adjacent research, drivers).
- The user wants agent-driven Ghidra/IDA/Hopper/Kupa analysis instead of manual xref spelunking.
- Building **offline / research** modifications where legality and ToS are the user's responsibility.

## Prerequisites

1. **Node.js** 22.19+, 24.11+, or 26+ with npm on the machine that holds target binaries (Windows: `npx.cmd` if execution policy blocks `npx`).
2. **REA wired to the harness**: `npx rea-agents@latest setup` (or `npx.cmd rea-agents@latest setup`), select Cursor (or other MCP-capable agent), approve install.
3. **One native engine installed**: Ghidra (free default), or IDA Pro, Hopper, or Kupa. REA needs a backend to drive.
4. Target files and game install path reachable from that machine.

## Agent workflow (in order)

1. **Scope** — Confirm target binary, architecture, and whether work is documentation-only, offline mod, or injection research. Do not assume undetected or live-server use.
2. **Baseline map** — Prompt pattern:
   - Analyze the executable: architecture, modules, symbols, interesting exports/imports.
   - Overview program structure and prioritized next investigations.
   - **Use REA** for evidence (disasm, xrefs, pseudocode), not guesses.
3. **System-by-system RE** — Pick one subsystem (entity list, renderer, glow, bone matrix, etc.). Ask the agent to name functions, retrieve pseudocode, follow call graph, and **cite addresses**.
4. **Living notes** — Maintain `docs/re-notes.md` (or repo equivalent):
   - Verified vs hypothesis
   - RVAs/addresses, struct layouts, vtables
   - Dead ends and retractions
5. **Reference harness** — If public outdated bases exist, attach as *structure hints only*; re-verify every path against the current binary via REA.
6. **Implementation** — Generate code only after steps 2–4. Human reviews assumptions, builds, and tests on **offline / controlled** targets first.

## Prompting and models

- Prefer **legitimate-framing** prompts for frontier models: "offline modification", "visibility bug investigation", "security research on owned copy" — not "make a cheat for live servers."
- **Orchestrator**: stronger reasoning model for planning and verification.
- **Discovery/implementation**: cheaper or less-restricted models when blocked; re-verify with the stronger model.
- Treat uncensored/local small models as high hallucination risk for assembly-heavy work.

## Quality bar (non-negotiable)

- Every claim ties to REA tool output (function name, address, xref, decompiler snippet).
- Reject confident struct layouts without sizeof/field evidence.
- Re-run analysis after game patches; addresses drift.

## Limitations (tell the user when relevant)

- AI RE is often wrong; detection bypass is a separate problem from feature dev.
- REA does not remove need for asm/debugger literacy — it shifts manual tool juggling to the agent.
- Engine, packers, kernel AC, and VM protect stacks need custom approaches.

## Quick checklist before declaring "done"

- [ ] Notes file updated with verified findings
- [ ] No unverified pointers in production paths
- [ ] Build instructions recorded for this repo
- [ ] User explicitly ok with test environment (not live anti-cheat evasion unless scoped)
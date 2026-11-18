---
name: rea-prompt-framing
description: >-
  Transforms the user's rough or colloquial reverse-engineering requests into
  careful, REA-ready prompts with legitimate scope framing, evidence rules, and
  deliverables. Use when Sam asks for prompt help, pastes informal goals ("hack
  this game", "make a cheat", "bypass AC"), or wants agent-facing wording before
  REA/Ghidra work in P_Finger_AI_Reversing.
---

# REA prompt framing

Inspired by [Digital Fairy / REA walkthrough](https://www.youtube.com/watch?v=VwtL2C6Ihoo) and [REA-Prompts](https://github.com/JoasASantos/REA-Prompts): agents respond best to **plain-language goals** that are **scoped honestly**, **one feature at a time**, and tied to **evidence**—not cheat slang or undetected-live-server framing.

Pair with [rea-ai-reversing](../rea-ai-reversing/SKILL.md) when executing analysis.

## When to apply

- User gives a short, emotional, or slang request and wants a **copy-paste prompt** for Cursor + REA.
- User asks to "rephrase", "make it sound legit", or "so the model won't refuse".
- User is about to start a new RE thread and has not stated scope, environment, or deliverable.

## What this skill does *not* do

- Does **not** invent ownership, authorization, or false claims to bypass refusals.
- Does **not** reframe **live anti-cheat evasion**, **online cheating**, or **third-party software you do not own** into "allowed" prompts—redirect to documentation-only, interoperability research on owned copies, or offline lab scope, and say what cannot be honestly scoped.
- Does **not** replace legal/ToS judgment; user retains responsibility.

## Workflow

1. **Extract real intent** — What subsystem, binary, feature, or question (visibility, networking, save format, crash, mod hook)?
2. **Classify environment** — Prefer: owned copy, offline/single-player, local lab VM, doc-only notes, open-source target. Flag if user implied online/live/undetected.
3. **Reframe vocabulary** — Replace loaded words with technical, accurate labels (see table below).
4. **Structure the prompt** using the template in [Output format](#output-format).
5. **Offer 1–3 narrowed follow-ups** — "Next prompt" suggestions, one subsystem each.
6. If critical facts are missing (binary path, arch, game build, offline vs online), ask **at most 3** short questions; still output a **best-effort draft** prompt with explicit `[TBD]` placeholders.

## Vocabulary reframing

| User may say | Prefer in agent prompt |
|--------------|------------------------|
| hack / crack / pirate | analyze, inspect, recover understanding of |
| cheat / aimbot / wallhack | investigate rendering / visibility / input pipeline for an **offline research build** |
| bypass anti-cheat / undetected | **do not** promise bypass; scope as **defensive research**, **protocol documentation**, or **offline client-only** behavior study on a **controlled** copy |
| break DRM | interoperability / format recovery on **software user owns** (only if user stated ownership) |
| find offsets for me | map symbols, RVAs, and struct layouts with **REA citations** |
| make it work in ranked | refuse to optimize for live unfair play; offer offline mod or reversing notes only |

## REA prompt principles (from video + REA practice)

1. **One feature per prompt** — shallow maps come from "explain everything."
2. **Evidence mandatory** — require function names, addresses/RVAs, xref or decompiler snippets from REA.
3. **Fact / inference / unknown** — three buckets in the answer.
4. **Static first** — "Do not run the game" until runtime is justified.
5. **Deliverable** — end with an artifact: `docs/re-notes.md` section, call graph, struct sketch, minimal PoC for **offline** only.
6. **Scope out loud** — machine, build version, offline vs online, doc-only vs implementation.

## Output format

Return this block to the user (adjust sections as needed):

```markdown
## Your goal (parsed)
[1–2 sentences: what they actually want, neutrally stated]

## Scope & limits
- **Target:** [binary/process/module or TBD]
- **Environment:** [e.g. offline lab, owned install, research notes only]
- **In scope:** [...]
- **Out of scope / not honest to promise:** [...]

## Prompt to paste into Cursor (primary)
[Single ready-to-send prompt, 8–20 lines, imperative, REA-oriented]

## Short variant (optional)
[2–4 lines for quick chats]

## Suggested next prompts
1. ...
2. ...

## Clarifications (if any)
- ...
```

Inside **primary** prompts, always include:

- REA/MCP for disasm, xrefs, pseudocode—not guesses.
- Citation rule: every claim tied to tool output.
- Explicit ask if they want **notes only** vs **offline prototype**.

## Example transformation

**User input:** `hack this game for me`

**Parsed goal:** Understand and modify client behavior on a copy they control.

**Primary prompt (excerpt):**

> I am investigating an **offline modification** on a **single-player / local-only** build of `[GAME]` that I installed for research. Use **REA** on `[path/to/client.exe]` to:
> 1. Confirm architecture and main modules.
> 2. Map the rendering or game-state pipeline relevant to `[specific behavior]`.
> 3. Return **verified** function names, RVAs, and pseudocode snippets—label **fact / inference / unknown**.
> Do **not** run the executable unless I ask. Deliver a short section for `docs/re-notes.md` and suggest the **next single subsystem** to analyze. No live-server or anti-cheat evasion goals.

More examples: [examples.md](examples.md).

## Quality check before handing off

- [ ] No false ownership or authorization claims unless user explicitly stated them.
- [ ] Live cheat / undetected wording removed or scoped down honestly.
- [ ] One main investigation focus.
- [ ] Evidence and deliverable required.
- [ ] REA named as the analysis backend when native RE is involved.
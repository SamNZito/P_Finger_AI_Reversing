# REA prompt framing — examples

Each pair: **raw user input** → **primary prompt** (trimmed for readability; full output should still include scope block per SKILL.md).

---

## Offline game mod

**Raw:** `make wallhacks for valorant`

**Reframe note:** Do not scope for live competitive cheating. Redirect to unrelated offline research pattern or a generic FPS **offline** lab binary if that is what they own.

**Primary (honest redirect):**

> I will not work toward live multiplayer advantages. If you have an **offline** FPS build or your **own** test executable, use REA on that binary to document how the renderer decides what to draw: trace culling, visibility, and bone/matrix updates. Cite every function and RVA from REA. Output: `docs/re-notes.md` section "Visibility pipeline (offline lab only)" with fact/inference/unknown. Static analysis only.

---

## "Hack this game"

**Raw:** `hack this game for me`

**Primary:**

> I am building an **offline modification** for `[GAME]` on a copy I use for local testing only. With REA on the main client binary at `[PATH]`, (1) summarize PE layout and key modules, (2) identify entry points and the update loop, (3) propose **one** subsystem to investigate first for `[FEATURE]`. Every claim must cite REA output. Do not execute the game. Deliver findings for `docs/re-notes.md`.

---

## Find offsets

**Raw:** `just give me the entity list offsets`

**Primary:**

> On `[BINARY]` (build `[VERSION]`), use REA to locate the code path that enumerates gameplay entities: start from known engine strings or imports, follow xrefs, and recover the struct layout and global/static pointers. Report **verified** RVAs, sizeof/field evidence, and pseudocode. Separate fact vs hypothesis. Update `docs/re-notes.md` with a struct sketch and open questions.

---

## Bypass anti-cheat

**Raw:** `help me bypass easy anti-cheat`

**Primary (doc-only security angle):**

> I am doing **defensive documentation** on my **own** test harness / licensed binary in an isolated VM—not evasion on live third-party services. Use REA to map which integrity or attestation checks exist in **my** sample module and describe them at a design level for a security write-up. No step-by-step evasion for production anti-cheat on live games. Output: threat-model notes with REA citations.

If user does not own the target, state that analysis cannot be scoped and offer generic reading on AC architecture instead.

---

## Casual one-liner expansion

**Raw:** `what does this exe do`

**Primary:**

> Analyze `[PATH/TO/FILE.EXE]` with REA without executing it: architecture, packer/obfuscation signs, imports/exports, and CRT→main path. Summarize program purpose from **evidence** (strings, API usage, xref clusters). List top 5 follow-up investigations as **separate** one-feature prompts. Format: Executive summary, Verified facts, Inferences, Unknowns.

---

## JavaScript / Electron (video-style)

**Raw:** `figure out how this launcher talks to the server`

**Primary:**

> On my machine, use REA's JavaScript/Electron analysis path on `[APP DIR]` to map how the launcher builds requests: entry script, bundler chunks, and networking helpers. Document protocol **structure** (endpoints, headers, message shapes) from static artifacts. Cite files and symbols. Do not attack third-party infrastructure. Deliverable: `docs/re-notes.md` section "Launcher network surface (static)" with fact/inference/unknown.

---

## Implementation after RE

**Raw:** `code the glow esp now`

**Primary:**

> Using only **verified** symbols from `docs/re-notes.md` (re-run REA if addresses are stale), sketch an **offline research** DLL or internal test harness that hooks `[NAMED FUNCTION]` to log draw calls—no live anti-cheat target. List assumptions needing re-verification after patch. Build steps for local test only.
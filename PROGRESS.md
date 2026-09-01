# Progress File — Anozin's USA Reimagined

This file exists so a new Claude session can pick up mid-project. Read this
first, then CLAUDE.md, before touching any files.

## Where the project stands

- Democratic tree and the Nationalist Communist tree are built out.
- Active work: the new **Nationalist** tree, split into two sub-paths —
  a calmer autarkic/isolationist path, and a civil-war/expansionist path.
  Each sub-path has its own foreign diplomacy branches (isolationist,
  hegemonic-Americas, side-with-a-WWII-faction, world conquest).
- The **FREE** (Federal Recovery Emergency Enterprise) mechanic is the
  signature system tied to the Nationalist tree: six categories (civilian
  industry, military industry, army, navy, air, science), each scored on a
  hidden points variable and expressed as a tier ladder of hidden ideas
  (`usa_free_<category>_si_-3` … `_si_3`), swapped in/out by scripted
  effects (`USA_AN_FREE_<cat>_up_<low|med|hih>`) rather than a normal
  dynamic-modifier scale. A FREE sub-tree of focuses (center = bigger
  magnitude, right = fewer downsides, left = better upsides) modifies it.
  See notes below for the full mechanism.
- The running in-repo TODO list lives at the top of
  [common/national_focus/usa.txt](common/national_focus/usa.txt) (lines
  ~19–48) — that list, not this file, is the authoritative backlog. Update
  it in place as items are finished or added; don't fork a second TODO list
  here.

## Session log

- **2026-08-31** — First session using this CLAUDE.md workflow. Did a
  read-only pass over the existing codebase (focus tree structure, the FREE
  mechanic's implementation across dynamic_modifiers/scripted_effects/ideas,
  events, decisions, localisation, scripted_triggers) to internalize style
  and design patterns before writing any new content. No game files were
  modified. Findings reported to the user in-chat.
- **2026-09-01** — Wrote up the prior session's findings as
  [STYLE_NOTES.md](STYLE_NOTES.md) at the repo root, so future sessions
  don't have to re-derive them from scratch. Read that file (alongside
  CLAUDE.md) at the start of any session before writing new focus/event/
  decision/idea/localisation content. Session closed with no other changes.

## Notes for future sessions

- Full style/pattern notes (FREE mechanic internals, focus/event/decision
  conventions, tooltip idioms, localisation conventions, indentation
  quirks) live in [STYLE_NOTES.md](STYLE_NOTES.md) — read it before writing
  new content, don't re-derive it from scratch.

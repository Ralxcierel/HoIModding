# CLAUDE.md — Anozin's USA Reimagined

## Project

Hearts of Iron IV mod called **Anozin's USA Reimagined**. It is an overhaul
mod for the United States, completely overhauling its focus tree and modifying
just about every other mechanic attached to it. It is designed to be mostly
plug-and-play with other mods, so it doesn't modify universal files that will
conflict with other mods (with a small handful of exceptions). The core of
the mod is a massive and complex tree for various different Democratic paths.
Recently, a Nationalist Communist tree was added, and now a Nationalist path
is being developed.

The general intention of the mod is to fix the United States within the game.
It hasn't seen any noteable updates or expansions in many years, and it's
also started to suffer from power creep from other nations who have been
recently updated. Part of the goal of the mod is to make the US even more
powerful than it is in the base game, but not to an un-fun level. The US
should properly feel like a final boss for the Allies in a historical run,
not something that can be easily matched by a competent minor nation.

The mod is well-acclaimed, with ~10k downloads, hundreds of comments, glowing
reviews, and 3 different indepedent translators. It has been a passion project
of mine for more than a year, and I intend to keep adding to and refining it
until the developers release their upcoming major update for the US. Even then,
I may continue to work on this mod.

The current goal is the completion of the new Nationalist trees. Broadly,
they follow along two different sub paths. The first is a calmer, internally-
focused, autarkic playstyle, while the other commits to a civil war and
expansionism. There are also different foreign diplomacy options available to
each, allowing players to stay more isolationist, go for hegemonic control
over the Americas, side with one of the major factions in WWII, or even
go down the route of a world conquest. One item present in the tree that
helps to define its character distinct from the other trees is the **FREE**
(Federal Recovery Emergency Enterprise) mechanic — a
dynamic modifier system with six categories (civilian industry, military
industry, army, navy, air, science), each with multiple tiers, plus a
sub-tree of focuses that scale and modify FREE's effects. Sub-tree branches:
center increases FREE's total effect magnitude, right reduces negative effects
from it, left improves positive effects.

## Workflow

### Before starting any task

1. Identify every file you expect to read or modify to complete the task
   (e.g., a focus that unlocks new decisions touches the focus tree file,
   the decisions file, and localisation).
2. List those files back to me, with a one-line reason for each, before
   opening or editing anything.
3. Wait for my go-ahead before proceeding.
4. If something mid-task doesn't match what you expected — an unfamiliar
   function, a system that doesn't behave the way you assumed — stop and
   flag it rather than guessing.
5. If a question ever comes up that doesn't already have an answer from
   me, pause and ask. Do not assume anything.

This applies at any scale, from a single focus to a whole new branch. The
point isn't process for its own sake — it's making sure I know what's being
touched before it happens, so I can redirect if something's about to go
sideways.

### Style

- Reference the STYLE_NOTES.md file at the root. This file contains guides
  for how to format and design your work. When new styles are recognized,
  ask about them and give me an option to add them to the guide.
- Match my existing code style exactly unless I say otherwise. Don't "clean
  up," refactor, or reorganize formatting, structure, or naming on your own
  initiative — even if you think it's an improvement.
- Before writing new content of a given type (focus, event, decision, etc.),
  look at the closest existing examples of that type first and match their
  structure, not just their syntax.
- Preserve my visual organization as-is: section dividers, spacing,
  ordering, grouping conventions.
- Some existing systems are built by rewiring/repurposing existing HoI4
  functions in non-standard ways on purpose. Don't "fix" these to be more
  conventional — treat unfamiliar-looking patterns as intentional unless I
  say otherwise.

### Comments

- Every new section of code gets a short comment explaining what it does
  and what it connects to (which focuses/events/decisions/effects it reads
  from or triggers).
- Match my existing comment style and formatting (dividers, header
  comments, inline vs. block) — pull from nearby existing code rather than
  defaulting to a generic style.
- Favor comments that help future debugging: what triggers this, what it
  depends on, what depends on it. Assume future-me is picking this up cold
  after time away and needs the comment to reorient fast.

### Progress-Keeping

- One of the main goals of this project is for Claude to be able to stop working
  on it and save its current work in a Progress file that a new instance of
  Claude will be able to read from and immediately pick up where the last
  instance left off.
- Every time you start working initially, go through the existing Progress file
  and make sure you understand where the project currently stands, then clean
  it and use it for the current session. As you work, leave short comments for
  that session logging steps. Once a session is finished, update the Progress file.
- If a Progress file does not exist when a session is started, create one.
- At the top of common/national_focus/usa.txt are a pair of sections detailing a
  'general to-do list' and a 'work to-do list'. Keep the former in mind, but the
  latter list is the primary focus of Claude Code's work.

## Reference

- Most localisation done in the mod is found in new_usa_ideas_l_english.
- As far as localisation goes, pay attention to the English folder. The other two
  are not up-to-date.
- Most work will occur in common/national_focus/usa, which is the file
  containing the central focus tree. The vanilla focus tree is included at the
  bottom of the file as a necessary measure for internal checks, but it
  has no effect and should not be modified.
- Many different related systems are overlapped inside of shared files.
  For instance, all new events are stored in events/USA_new_events, within which
  they are organized.
- Almost every single file in the mod is either a full replacement of a
  counterpart in the game, a completely new file, or a file whose contents
  are added additively to existing logic in the game. This is so that the mod
  is compatible with most other mods, allowing it to be a single-fit item
  that changes how the USA functions within a player's game.

## What NOT to do

- Don't restructure or refactor existing systems without being asked.
- Don't invent new naming or ID conventions — follow what's already there.
- Don't skip the file-list-and-confirm step, even for small tasks.
- Don't modify the vanilla focus tree at the bottom of common/national_focus/usa.

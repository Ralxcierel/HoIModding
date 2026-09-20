# Progress File — Anozin's USA Reimagined

This file exists so a new Claude session can pick up mid-project. Read this
first, then CLAUDE.md, before touching any files.

## Where the project stands

- **First draft of the Golden West Update is complete** (as of 2026-09-19,
  confirmed by the user). All three trees — Democratic, Nationalist
  Communist, and the new **Nationalist (AN)** tree (including its Southern
  Crisis branch, the FREE sub-tree, and both new AN industry trees, Crisis
  of Dependency and Kickstart the Steel Belt) — are fully written and
  wired: titles, descriptions, and completion_reward effects are in place
  mod-wide. The `TO-COMPLETE`/`§CTO-COMPLETE§!` placeholder convention
  mentioned in older entries below is now resolved (a mod-wide grep found
  exactly one leftover placeholder, an orphaned/unused `TBA_TT` key — see
  backlog).
- **The project is now in the bugfixing/localisation/playtesting phase**,
  not active feature development. The next milestone is translation +
  external playtesting, so the current priority is fixing the issues found
  in the 2026-09-19 full-mod QA pass (below) rather than writing new
  content.
- The **FREE** (Federal Recovery Emergency Enterprise) mechanic is the
  signature system tied to the Nationalist tree: six categories (civilian
  industry, military industry, army, navy, air, science), each scored on a
  hidden points variable and expressed as a tier ladder of hidden ideas
  (`usa_free_<category>_si_-3` … `_si_5`), swapped in/out by scripted
  effects (`USA_AN_FREE_<cat>_up_<low|med|hih>`) rather than a normal
  dynamic-modifier scale. A FREE sub-tree of focuses (center = bigger
  magnitude, right = fewer downsides, left = better upsides) modifies it.
  Full mechanism documented in [STYLE_NOTES.md](STYLE_NOTES.md).
- The running in-repo general TODO list lives at the top of
  [common/national_focus/usa.txt](common/national_focus/usa.txt) (lines
  ~19–48) — that list, not this file, is the authoritative feature backlog.
  This file's backlog (below) is the **QA/bugfix** backlog specifically.
- **HoI4 focus `prerequisite` semantics, confirmed by the user** (a prior
  session mis-analyzed this — don't repeat the mistake): within a single
  `prerequisite = { focus = A focus = B }` block, the focuses are OR'd (any
  one satisfies it). Separate `prerequisite = { ... }` blocks on the same
  focus are AND'd together (every block must be satisfied). This is the
  opposite of `mutually_exclusive`, which excludes every listed focus
  outright. Get this right before reasoning about tree reachability.

## QA backlog (from the 2026-09-19 full-mod audit) — NOT YET FIXED

A full-mod QA pass was done in chat on 2026-09-19 ahead of the translation/
playtesting handoff: automated structural cross-referencing (duplicate/
dangling ids across focuses, events, decisions, ideas, dynamic modifiers,
opinion modifiers, leader traits, gfx icon references) plus two rounds of
agent-driven review (focus-tree logic/balance on the newest AN trees, and a
two-pass localisation grammar + flavor edit of all 7 English `.yml` files).
None of these have been fixed yet — this is the punch list for whoever picks
this up next. Full detail (exact quotes, line-by-line grammar fixes, and
~20 concrete flavor rewrites) is in that day's chat transcript; the items
below are the durable summary.

### Logic bugs (confirmed, need script fixes)

- **`USA_new_lend_lease_act`** ([usa.txt:6222,6254,6262](common/national_focus/usa.txt:6222)):
  checks `has_completed_focus = USA_neutrality_act` / `USA_limited_intervention`
  — those bare ids only exist in the inert vanilla tree kept at the bottom
  of the file (per CLAUDE.md, "should not be modified" — but also should
  not be *referenced* by live logic). The real modded focuses are
  `USA_new_neutrality_act` / `USA_new_limited_intervention`. Effect: the
  completion_reward's path-specific branch always falls through to
  `else`, granting `USA_lend_lease_act_dummy` (an idea whose own comment
  says "should never get here, just for tooltip purposes") to every
  player instead of the correct path idea (`_li` / `_na` / `_aat`); an
  `available` sub-condition also silently never applies. Fix: swap in the
  `_new_`-prefixed ids in both the `available` and `completion_reward`
  blocks.
- **Same mistake in an on_action** — [new_usa_on_actions.txt:45-46](common/on_actions/new_usa_on_actions.txt:45):
  after the Democratic USA regains control post-NC-civil-war,
  `unlock_national_focus`/`complete_national_focus` target
  `USA_continue_the_new_deal` (vanilla-only) instead of
  `USA_new_continue_the_new_deal`.
- **FREE sub-tree: circular `available` dead-end.** `USA_lady_libertys_arsenal`'s
  `available` ([usa.txt:36615](common/national_focus/usa.txt:36615)) requires
  `has_completed_focus = USA_FREE_recovery_achieved`, while
  `USA_FREE_recovery_achieved`'s own `available` ([usa.txt:37417](common/national_focus/usa.txt:37417))
  requires `has_completed_focus = USA_lady_libertys_arsenal` right back —
  each waits on the other, so neither can ever be completed first. (Note:
  this is *not* a `prerequisite`-block issue — both blocks are affected by
  the fix above being applied correctly, this is a separate `available`
  trigger problem. The reachability via `prerequisite` chains for these two
  focuses is actually fine once you apply the corrected OR/AND semantics
  noted above — don't re-flag that part.) Needs one side's
  `has_completed_focus` condition removed, reordered, or replaced.
- **Crisis of Dependency capstone reward is a tautology** —
  `USA_north_american_autarky`'s completion_reward ([usa.txt:39103-39181](common/national_focus/usa.txt:39103))
  gates its three power tiers ("Quick"/"Medium"/"Long") behind
  `NOT = { has_completed_focus = A has_completed_focus = B }` for the
  *other two* mutually-exclusive path focuses. Since A and B can never
  both be true, `NOT(A AND B)` is always true, so **all three tiers fire
  for every player**, stacking every economic bonus and showing all three
  tooltip blocks regardless of which timing path was actually taken. The
  original, correct logic (a direct `has_completed_focus = <that path>`
  check) is still sitting commented-out immediately above the broken
  version — this looks like a botched refactor. Fix: restore the direct
  positive checks.
- **4 dead conditional effects** (harmless but leftover/copy-paste,
  low priority): `USA_stars_on_the_horizon`, `USA_american_wellspring_administration`,
  `USA_american_architectural_administration`, and `USA_industrial_funding`
  each have a `hidden_effect` gated on completing **both**
  `USA_american_architectural_administration` **and**
  `USA_american_wellspring_administration` — impossible, they're mutually
  exclusive. `USA_stars_on_the_horizon` also has an unconditional trigger
  for the same event so player-facing behavior is unaffected, but all 4
  are dead code worth cleaning up (likely meant `OR`).
- **`USA_every_chip_down`** ([usa.txt:10450](common/national_focus/usa.txt:10450)):
  unlocks/activates decision `women_in_the_workforce`, which has zero
  definition anywhere in the mod (not in any decisions file, no
  localisation). Either write the decision or fix the reference.
- **3 leader traits used but never defined**: `communist_revolutionary`,
  `democratic_reformer`, `fascist_demagogue` in
  [common/characters/USA.txt](common/characters/USA.txt) (lines 139, 1743,
  1776) — same custom ideology-conditional-trait pattern as
  `american_fuhrer`/`wallstreet_leader`/`goldstar_phoenix` (which *are*
  properly defined), just missing from
  [new_USA_traits.txt](common/country_leader/new_USA_traits.txt).
- **7 focus icons reference undefined GFX sprites** (will render as
  missing-texture placeholders): `USA_new_build_the_pentagon`,
  `USA_write_war_plan_diamond`, `USA_fell_the_warring_giants`,
  `USA_the_american_dream`, `USA_democracys_vanguard` (icon name doesn't
  even thematically match the focus — likely a copy-paste leftover from a
  different focus), `USA_restore_the_gold_standard`,
  `USA_representation_reform`. Either add the missing `SpriteType` entries
  (with art) to `interface/new_usa_goals.gfx`, or point these at an
  existing icon.

### Localisation bugs (functional, not just wording)

- **Duplicate loc key silently breaks two focus descriptions** —
  [new_usa_focus_l_english.yml:1499 & 1502](localisation/english/new_usa_focus_l_english.yml:1502):
  `USA_propose_canadian_unification_desc` is defined twice. The second
  definition (helicopter-flavored text clearly meant for
  `USA_rotory_innovations_desc`) silently overwrites the first. Net
  effect: "Propose Canadian Unification" shows the wrong (helicopter)
  description, and "Rotory Innovations" (also misspelled — should be
  "Rotary") has no description at all. Fix: rename the key on line 1502.
- **Malformed quoting**: `USA_the_flying_tigers: ""The Flying Tigers""`
  ([new_usa_focus_l_english.yml:947](localisation/english/new_usa_focus_l_english.yml:947))
  — doubled quotes will break rendering of that focus's title. Fix to a
  single pair of quotes.
- **Hardcoded wrong scope tag**: `new_usa_bargain_canada.1.desc`
  ([event_usa_mod_l_english.yml:75](localisation/english/event_usa_mod_l_english.yml:75))
  uses `[ENG.GetAdjective]` while every sibling entry in the same chain
  correctly uses `[FROM.GetAdjective]`. If whoever currently holds Canada
  isn't England, this line will wrongly say "the English government."
  Fix: change `ENG` to `FROM`.
- Several unescaped internal quotes (`"..."` instead of `\"...\"`) inside
  flavor text — risk of parser breakage or garbled rendering. Clustered
  instances: `new_usa_pacify_mexican_turmoil.1.desc`/`.3.desc`,
  `new_usa_preempt_a_southern_invasion.1.desc`,
  `USA_alliance_with_a_united_china_desc`. The mod escapes quotes
  correctly elsewhere, so this is an inconsistency, not a style choice —
  worth a global grep-and-fix pass rather than one-off edits.
- **Possible lead on a pre-existing known bug**: the General Mod To-Do
  List at the top of this file (line ~49) already notes "Bury the
  robberbarons is causing crazy errors when hovered over." The QA pass
  found `USA_bury_the_robberbarons`'s title/desc spelled inconsistently
  ("Robberbarons" vs. "Roberbarons") — checked the focus's own
  `completion_reward` ([usa.txt:11827-11844](common/national_focus/usa.txt:11827))
  and its `USA_NC_increase_conservative_resistance` scripted effect, and
  neither has an obvious syntax break, so the loc mismatch may or may not
  be the actual cause of the hover error. Worth a closer look (test
  in-game with the loc fix applied first) before assuming it's fixed.

### Content gaps (empty, not logic bugs — need real content written)

8 focuses have **no title, no description, and no completion_reward**
anywhere, despite being fully wired into the tree with real
prerequisites/positions (lines ~3640–4552): `USA_expanded_income_tax`,
`USA_fair_shares`, `USA_general_welfare`, `USA_universal_income`,
`USA_pursue_a_better_tomorrow`, `USA_seize_corporate_industries`,
`USA_for_the_people`, `USA_the_scarlet_eagle_rises`. These will show up
in-game as blank/untitled, functionless focuses right now.

(`USA_the_bordeaux_conferance` is also empty, but its surrounding comment
still frames it as a conceptual planning stub, so likely already known.
`USA_those_the_revolution_forgot` and
`USA_SM_NC_decrease_conservative_resistance` are empty too but are
explicitly `[unused]`/`allow_branch = always = no` — intentional, not
bugs, leave them.)

### Localisation grammar/spelling and flavor/word-choice

Two full passes were done over all 7 English `.yml` files (all clean:
`new_usa_countries_cosmetic`, `new_usa_factions`, `new_usa_techs`; the
rest had findings). Roughly **60 concrete spelling/grammar fixes** (things
like `beligerant`→belligerent, `reigns`→reins, `tyrrany`→tyranny in 3+
places, `Phillipines`→Philippines, several has/have subject-verb
mismatches copy-pasted across multiple event entries, British
"labour"/American "labor" inconsistency including in the historical focus
name `USA_new_fair_labour_standards_act` which misspells the real 1938
law) and **~20 flavor/word-choice rewrites** (flat "mechanical readout"
focus descriptions that just restate the effect instead of speaking in
character, several repeated sentence-openers/templates copy-pasted across
sibling focuses or event reaction paragraphs, a few anachronistic/modern
word choices breaking the 1940s voice) were catalogued with exact
key/line references and suggested rewrites in that day's chat transcript.
Re-run the same two-pass audit (structural grep first, then a
general-purpose agent per major loc file) if that transcript isn't
available to whoever picks this up.

### Needs a design decision, not a confident bug call

Files that read as largely-untouched vanilla/DLC baseline
(`events/USA.txt`, `events/MTG_USA.txt`, `common/decisions/USA.txt`,
`common/decisions/MTG_congress.txt`) contain roughly **110 conditions
across ~30 focus ids** using the same "bare vanilla id instead of the
`_new_`-prefixed modded one" pattern as the two confirmed bugs above
(e.g. `has_completed_focus = USA_voter_registration_act`,
`USA_war_plan_red`, `USA_focus_on_europe`). Unclear whether this is
intentional dormant vanilla content (normal modding practice — these
files are kept present without every internal check needing to reference
the modded tree) or content that should still be reachable and needs
updating. Ask the user before touching any of these — do not bulk-fix
without confirmation, the scope is large and the vanilla-tree-preservation
convention is deliberate per CLAUDE.md.

## Notes for future sessions

- Full style/pattern notes (FREE mechanic internals, focus/event/decision
  conventions, tooltip idioms, localisation conventions, indentation
  quirks) live in [STYLE_NOTES.md](STYLE_NOTES.md) — read it before writing
  new content, don't re-derive it from scratch.
- Reminder for any future auto-progression chain: HoI4 focus-tree constants
  (`common/national_focus/usa.txt`) don't carry over into
  `events/USA_new_events.txt` — every `@timer` an event schedules off needs
  its own mirrored constant declared at the top of that file, in **days**
  (weeks×7), not weeks.
- Reminder on `prerequisite` semantics (see "Where the project stands"
  above) — a prior session got this backwards mid-project; if anything in
  chat history or old commit messages talks about a focus being
  "unreachable" due to a multi-`focus=` prerequisite block, re-verify it
  against the corrected rule before trusting it.
- The detailed, focus-by-focus development history of how the Nationalist
  tree, Southern Crisis, the AN industrial-recovery core, and Crisis of
  Dependency were originally built (including the dynamic-modifier
  value-tier framework: which variables are "extremely valuable" vs.
  "strong" vs. "minor," the "no weekly modifiers" rule, the multi-family
  stacking/trade-off content standard, and the tally-script approach used
  to balance-check new trees against `usa_industrial_recovery`/`_NC`) has
  been trimmed from this file now that all of it has shipped in the first
  draft — see git history / STYLE_NOTES.md if that design reasoning is
  needed again for a similar future tree. The **current** actionable
  backlog is the QA section above.

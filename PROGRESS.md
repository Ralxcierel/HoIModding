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
- The running in-repo general TODO list lives at the top of
  [common/national_focus/usa.txt](common/national_focus/usa.txt) (lines
  ~19–48) — that list, not this file, is the authoritative backlog. Update
  it in the Work section as items are finished or added; don't fork a second TODO list
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
- **2026-09-01** — Filled in the automatic-progression logic for the
  **Southern Crisis** tree (`USA_southern_crisis`'s 6 child stub focuses +
  `A Golden West`'s capstone). Traced the exact mechanism from the AFYP
  "Red July" chain (`events/USA_new_events.txt` `new_usa_nc_red_july_1`–`5`,
  focuses `USA_labor_councils` → `USA_graduated_taxation` →
  `USA_representation_reform`/`USA_property_reform` →
  `USA_american_economic_industrial_organization`/
  `USA_soviet_style_policies`) and mirrored it 1:1:
  - New `usa.txt` constants block "Southern Crisis Timers"
    (`@national_character_timer`, `@new_frontier_timer`, `@autarky_timer`,
    `@new_america_timer`, `@brotherhood_timer`) — seeded with the same
    week-values as the Red July timers per user instruction; retune later.
    HoI4 constants don't cross files, so `events/USA_new_events.txt` needs
    its own mirrored copy of every timer used by an event — added a
    matching "Constants for Southern Crisis" block there (day-values,
    weeks×7, same as the other four mirrored blocks already in that file).
  - `USA_banner_clubs` → `USA_the_question_of_our_national_character`
    (branch decision) → `USA_visions_of_a_new_frontier`/`USA_a_new_america`
    (mutually exclusive) → `USA_state_autarky_movement`/
    `USA_brotherhood_spirit` → `USA_a_golden_west` (already correctly
    OR-gated on either branch's final focus, untouched).
  - New event namespaces `new_usa_southern_crisis_1`–`5` in
    `events/USA_new_events.txt`, new flags `running_qonc_flag`,
    `running_vnf_flag`, `running_ana_flag`, `running_sam_flag`,
    `running_bhs_flag`, and branch-choice flags `SC_new_frontier_chosen_flag`/
    `SC_new_america_chosen_flag`.
  - Deliberately did **not** write any actual completion_reward game
    effects (PP/stability/eco variables/ideas) or narrative flavor — user
    asked for progression logic only, content to follow in a later pass.
  - Added the 3 required gating tooltip keys
    (`USA_southern_crisis_in_progress_tt`/`_cant_do_tt`/
    `_cant_do_exclusive_tt`) to `new_usa_ideas_l_english.yml` with generic
    mechanical text.
  - All focus titles/descs and the new choice event's text
    (`new_usa_southern_crisis_1.2.*`) were left as literal `"TO-COMPLETE"`
    placeholders (not omitted) per the new STYLE_NOTES.md convention, so a
    future localisation pass can grep for `TO-COMPLETE` and find every
    pending block. Search for it before considering this tree done.

- **2026-09-02** — User confirmed the Southern Crisis chain works correctly
  in-game (tested with reduced timers — see note below on `usa.txt`'s
  Southern Crisis Timers block, its week-values are currently 10 across the
  board for that test, with the real 22/40/40/40/48 values preserved as
  commented-out lines above them; restore those before release). Followed
  up with the "core"
  industrial-recovery chain for the Nationalist (AN) tree:
  `USA_industrial_recovery_AN` → `USA_stars_on_the_horizon` →
  `USA_the_eagle_lifts_its_wings`. Mimicked the Nationalist Communist
  equivalent directly (`USA_industrial_recovery_NC` → `USA_red_new_deal` →
  `USA_engines_of_industry`, `events/USA_new_events.txt` namespaces
  `new_usa_red_new_deal`/`new_usa_engines_of_industry`), which turned out to
  use the same shine/`complete_national_focus` mechanism as Southern Crisis
  but without a branching choice — single linear chain, one
  `has_country_flag = ..._in_progress_flag` gate per step, no
  "exclusive"/sibling-branch tooltip needed.
  - New `usa.txt` week-constants `@stars_on_the_horizon_timer` (30) and
    `@eagle_lifts_its_wings_timer` (20) under a new "American Nationalist"
    subsection of Auto Focus Timers, values copied directly from
    `@red_new_deal_timer`/`@engines_of_industry_timer`.
  - Matching day-value mirror block added to `events/USA_new_events.txt`
    (210/140 days) — remember constants don't cross files in HoI4; every
    chain needs its own copy of the timers it calls, in days there since
    the events schedule off raw day counts, not focus-cost weeks.
  - `USA_industrial_recovery_AN` completion_reward now fires
    `new_usa_stars_on_the_horizon.1`; `USA_stars_on_the_horizon` fires
    `new_usa_the_eagle_lifts_its_wings.1` on completion. Chain stops at
    Eagle per user's scope — `USA_lady_libertys_arsenal` (the next focus
    down) untouched.
  - New flags: `USA_stars_on_the_horizon_in_progress_flag`,
    `USA_the_eagle_lifts_its_wings_in_progress_flag`.
  - Added the 4 required gating tooltip loc keys
    (`USA_stars_on_the_horizon_in_progress_tt`/`_cant_do_tt`,
    `USA_the_eagle_lifts_its_wings_in_progress_tt`/`_cant_do_tt`) to
    `new_usa_ideas_l_english.yml` — both focuses already had real titles,
    so no `TO-COMPLETE` placeholders were needed here (descs were already
    blank pre-existing, left as-is). No completion_reward game effects
    added for Stars on the Horizon or Eagle Lifts its Wings — progression
    wiring only, matching the Southern Crisis precedent.

## Notes for future sessions

- Full style/pattern notes (FREE mechanic internals, focus/event/decision
  conventions, tooltip idioms, localisation conventions, indentation
  quirks) live in [STYLE_NOTES.md](STYLE_NOTES.md) — read it before writing
  new content, don't re-derive it from scratch.
- Southern Crisis tree logic is done and **verified working in-game** by the
  user. Still needed: actual completion_reward effects for its 6 focuses
  (currently only set flags/fire chain events, no game-balance content),
  restoring the real (non-test) timer values in `usa.txt`'s Southern Crisis
  Timers block, and all `TO-COMPLETE` localisation (grep for it — focus
  titles/descs for `USA_banner_clubs` through `USA_a_golden_west`, and the
  `new_usa_southern_crisis_1.2` choice event's text).
- The AN industrial-recovery core (`USA_industrial_recovery_AN` →
  `USA_stars_on_the_horizon` → `USA_the_eagle_lifts_its_wings`) has its
  auto-progression logic done, mirroring the NC equivalent
  (`USA_industrial_recovery_NC` → `USA_red_new_deal` →
  `USA_engines_of_industry`). No completion_reward game effects added yet
  for the latter two, and `USA_lady_libertys_arsenal` (the next focus past
  Eagle) hasn't been touched — still a stub.
- Reminder for any future auto-progression chain: HoI4 focus-tree constants
  (`common/national_focus/usa.txt`) don't carry over into
  `events/USA_new_events.txt` — every `@timer` an event schedules off needs
  its own mirrored constant declared at the top of that file, in **days**
  (weeks×7), not weeks.

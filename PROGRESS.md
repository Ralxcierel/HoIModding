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

- **2026-09-02 (cont.)** — Filled in `completion_reward` for all 20 focuses in
  the **Crisis of Dependency** tree (`USA_crisis_of_dependency` through
  capstone `USA_north_american_autarky`, `common/national_focus/usa.txt`
  ~lines 35362–36259), the first of the two new AN industry trees (the
  other, `USA_kickstart_the_steel_belt`, is still a stub — next session).
  This tree's skeleton (left = 3-way short/medium/long investment choice,
  right = infrastructure-vs-resources split converging on a mutually
  exclusive `American Wellspring Administration`/`American Architectural
  Administration` pick, both sides feeding the capstone) was already laid
  out; only `completion_reward` bodies were empty.
  - Per user direction, this tree's economic payoff runs through the
    **FREE mechanic** rather than direct eco-variable edits (the way
    `usa_industrial_recovery`/`_NC` do) — confirmed precedent for this by
    grepping every `USA_AN_FREE_*_up_*` call already in the built-out AN
    sci/mil/army/navy/air sub-trees: one tick per focus, tier (`low` →
    `med` → `hih`) scaling with the focus's depth in its branch. This is
    also the **first tree to use the `civ` FREE category** — every
    prior AN focus tying into FREE used mil/arm/nav/air/sci, `civ` was
    otherwise only touched by the `USA_FREE_CHECK` debug focus. One
    exception: `USA_increase_technological_funding` (long-path capstone)
    calls `sci_up_low` instead of `civ`, since it's explicitly about
    tech/science funding — this differentiates the long path as touching
    2 FREE categories total vs. the short/medium paths' 1, on top of its
    higher total tick count, matching CLAUDE.md's "longer choice = more
    total value" framing.
  - Light thematic dressing layered on top of the FREE tick per focus
    (matched to each focus's name/icon): `add_political_power` on
    PP/administration-flavored focuses, `add_building_construction`
    (infrastructure or industrial_complex via `random_owned_controlled_state`)
    on infrastructure/construction-flavored focuses, `add_resource` on the
    mining/refinery-flavored focuses (`USA_finance_new_mines_and_refineries`,
    `USA_tap_old_gold_mines` — the latter reuses **The West**'s icon
    (`GFX_focus_generic_expand_gold_mining`), which was the explicit
    precedent mirrored for its resource-add idiom, scaled down since it's a
    single leaf focus vs. The West's tree-opener scale). Capstone
    (`USA_north_american_autarky`) is the largest payoff: 50 PP, 2x
    instant-build industrial_complex level 2, `civ_up_hih`.
  - Localisation: found all 20 focuses already have real titles in
    [new_usa_focus_l_english.yml](localisation/english/new_usa_focus_l_english.yml)
    (not `new_usa_ideas_l_english.yml` — that was a wrong assumption
    corrected mid-session) but empty `_desc` strings. Per user's choice,
    filled every one with the literal `"§CTO-COMPLETE§!"` placeholder
    (STYLE_NOTES.md convention) rather than writing real flavor text —
    grep for it when a later localisation pass comes through. No new
    `custom_effect_tooltip`/gating-tooltip keys were needed since the
    `USA_AN_FREE_*` scripted effects carry their own tooltips internally.
  - Did **not** touch `available` blocks (left empty, as they were) —
    scope was completion effects only, per the user's request.

- **2026-09-02 (cont. 2)** — User flagged that the Crisis of Dependency tree
  (just filled in above) was "well under 1/10 the weight" of
  `usa_industrial_recovery`/`_NC` in total scale, and asked for it to be
  upscaled to roughly match them. Quantified this properly instead of
  eyeballing it: wrote a Python tally script
  ([tally.py](file:///C:/Users/homes/AppData/Local/Temp/claude/C--Users-homes-Documents-Paradox-Interactive-Hearts-of-Iron-IV-mod-anozinsUSAReimagined/7bf76af8-d434-4bc4-a6da-782de8bb3cc6/scratchpad/tally.py),
  session scratchpad — not part of the mod) that regexes every
  `completion_reward` in a given line range and sums `add_political_power`,
  `add_building_construction` (by type/level), `add_extra_state_shared_building_slots`,
  `add_resource` (by type), and every `add_to_variable` touching a `USA_*`
  dynamic-modifier variable. Ran it over `usa_industrial_recovery` (lines
  27851–31943, 36 focuses) and `usa_industrial_recovery_NC` (31944–34421,
  32 focuses) to get real baseline numbers — see the session's tool output
  for the full breakdown; headline figures: Dem = 1410 PP / 69 civ-factory
  levels / 147 shared building slots / 146 resources / ~28 eco-variable
  touches; NC = 690 gross PP / 44 civ levels / 67 slots / 0 resources / ~29
  eco-variable touches. The original Crisis pass (my initial completion_reward
  fill-in) only had 265 PP / 9 civ levels / 4 slots / 18 resources / **zero**
  eco-variable touches — confirmed the "1/10" complaint, and the total
  absence of `add_to_variable` economy modifiers was the biggest gap (every
  focus in both reference trees leans on that system, Crisis had none of it).
  - Rewrote every one of the 21 focuses' `completion_reward` blocks to
    close that gap: paired every `add_building_construction` with the
    `add_extra_state_shared_building_slots = 2` companion effect (an
    idiom both reference trees use on nearly every building block, which
    the first pass had missed except on the capstone), bumped several
    industrial_complex additions from level 1 to level 2 / from 1 block to
    2 per focus, roughly doubled most resource amounts, and added a real
    `USA_eco_dy_*` variable touch (via `usa_modify_eco = yes` +
    `add_to_variable` + the matching pre-existing `..._tt` tooltip key —
    confirmed the correct variable→tooltip pairings by regexing them out of
    the two reference trees rather than guessing) to about half the
    focuses: `consumer_goods_factor`, `global_building_slots_factor`,
    `local_resources_factor`, `research_speed_factor`,
    `production_speed_industrial_complex_factor`,
    `production_speed_buildings_factor`,
    `production_factory_efficiency_gain_factor`, `economy_cost_factor`,
    `stability_factor` — picked per focus's theme, same as the FREE-tick
    assignment from the prior pass.
  - Re-ran the tally after editing: Crisis is now 590 PP / 34 civ-factory
    levels / 15 infrastructure levels / 58 shared slots / 61 resources / 9
    distinct eco-variables (15 touches). On a **per-focus** basis (21
    focuses vs. Dem's 36 / NC's 32) this lands right between the two
    reference trees on every metric — e.g. civ-factory levels/focus: Dem
    1.92, Crisis 1.62, NC 1.375 — which is the fair bar given Crisis has
    fewer, denser focuses rather than more numerous small ones. Crisis also
    still carries the FREE-tick layer neither reference tree has, on top of
    this.
  - Verified brace balance across the whole `usa.txt` file after all edits
    (14,614 open / 14,614 close) — no structural breakage introduced.
  - This same under-scaling problem will apply to `USA_kickstart_the_steel_belt`
    when that tree gets its completion-effects pass — budget for a similar
    tally-and-scale exercise then, not just a first-draft fill-in.

- **2026-09-02 (cont. 3)** — Even after the tally-based rescale, the user
  said Crisis of Dependency was still "drastically underpowered." Rather
  than rescaling again blind, did a **path-aware** tally (accounting for
  `mutually_exclusive` branches — not every focus in a tree is reachable in
  one playthrough) over `usa_industrial_recovery`/`_NC`, extending
  `tally.py` into `path_tally.py` (same scratchpad) which parses real
  `prerequisite`/`mutually_exclusive` graph structure, enumerates every
  valid combination of exclusive choices, and reports true min/max ranges
  per metric. Findings (full breakdown in that turn's tool output): Crisis's
  raw PP/civ-factories/resources were now in a reasonable range (35-95% of
  Dem/NC per-focus), but **economy dynamic-modifier magnitude was still only
  ~8-13%** of the reference trees, and Crisis had zero unique ideas, zero
  events, zero special dynamic modifiers, zero mil/dock factories (the last
  by design — Crisis is civ-only, Kickstart the Steel Belt should cover
  mil/naval).
  - This prompted the user to hand-write and finish the first two focuses
    themselves (`USA_crisis_of_dependency`, `USA_the_bay_otzen_plan`) as a
    concrete style target, then explain the *real* power hierarchy behind
    HoI4's dynamic-modifier variables — which my flat "1 variable per focus"
    approach had been completely blind to:
    - **Extremely valuable**: `USA_gov_dy_min_export` (= "resources to
      market," lower is better), `USA_eco_dy_global_building_slots_factor`
      (= "max factories per state" — the *dynamic modifier*, not the
      one-off `add_extra_state_shared_building_slots` flat effect I'd been
      leaning on), `research_speed_factor`, the `production_factory_*_efficiency`
      variables, `industrial_capacity_factory` ("factory output").
    - **Strong**: `consumer_goods_factor`, `production_speed_buildings_factor`
      ("construction speed"), `local_resources_factor` ("resource gain"),
      `USA_dy_mic_special_project_speed_factor`.
    - **Minor**: most others, explicitly including flat `add_political_power`
      and `add_stability` — the two things my original design leaned on
      hardest.
    - User explicitly said **do not use any "weekly" modifiers**
      (`stability_weekly_factor`, `weekly_casualties/convoys/bombing_war_support`)
      in this tree — skip that whole family.
    - Confirmed via [history/countries/USA - USA.txt](history/countries/USA%20-%20USA.txt:1526)
      that the US's dynamic-modifier variables start with real malices baked
      in (`consumer_goods_factor` starts at **+0.6**, `local_resources_factor`
      at **-0.20**, `conscription_factor` at **-0.50**, plus MIC/army/navy/air
      penalties) — the core path (not this tree) is responsible for clawing
      those back, so don't chase "undoing the malice," judge focuses on the
      tiers above instead.
  - The two user-written focuses also demonstrated a much richer content
    pattern than my rescale used: **multiple dynamic-modifier families
    stacked per focus** (Bay-Otzen uses `usa_modify_eco` for 4 variables
    *and* `USA_modify_gov` — a third wrapper family, gating
    `USA_government_dynamic_modifier`, that I hadn't discovered — for 2
    more), **real trade-offs** (Bay-Otzen costs `political_power_factor
    -0.10`, `min_export -0.15` — a downside AND the single most valuable
    lever at once, since it's simultaneously "the cost" and "the point" —
    plus opinion penalties with every other country, worse from
    democracies), a **new `add_timed_idea` national-spirit pattern** (bespoke
    idea defined in `common/ideas/new_usa_ideas.txt`, `# AMERICAN NATIONALIST`
    section, named `usa_<focus>_ti`, one-line loc entry in
    `new_usa_ideas_l_english.yml`, no `_desc` needed), a **diplomatic
    dimension** via `common/opinion_modifiers/new_usa_opinions.txt`, cost
    **halved to 5** (was 10), richer `search_filters`, and a real `available`
    gate (`congress_low_support_trigger = yes` — an existing base-game/mod
    Congress mechanic used throughout the tree, not invented).
  - Adopted a new workflow going forward: work through the remaining 19
    focuses **one at a time**, prompting the user for that specific focus's
    goal/tone before writing it, rather than batch-designing blind. Finished
    to this new standard in this session:
    - `USA_invest_in_infrastructure` — 2x instant infrastructure-2 builds,
      `global_building_slots_factor +0.10` (extremely-valuable tier),
      `production_speed_buildings_factor +0.05` /
      `local_resources_factor +0.05` (strong tier), new idea
      `usa_invest_in_infrastructure_ti` (180 days: `production_speed_buildings_factor
      +0.10` / `consumer_goods_factor +0.03` cost).
    - `USA_reinforce_interstate_routes` — 4x infrastructure + 2x
      `supply_node` instant builds (state-scoped, no hardcoded province —
      confirmed this is valid HoI4 syntax; the mod's only other
      `supply_node` precedent hardcodes specific provinces for a
      fortification-line focus and wasn't the right pattern to copy here),
      another `production_speed_buildings_factor +0.05` stack, slight
      `+15 PP` / `+0.02 stability` (correctly kept minor per the tiering),
      new idea `usa_reinforce_interstate_routes_ti` (150 days:
      `production_speed_buildings_factor +0.10` / `political_power_gain
      -0.05` cost).
  - **Session ended mid-focus**: I had just asked the user for goal/tone on
    **focus #5, `USA_finance_new_mines_and_refineries`** (opens the
    resources branch: → `Tap Old Gold Mines`/`Establish New Forestry Zones`
    → `The Continental Grid` → `American Wellspring Administration`) when
    they asked to wrap the session — no answer was given, nothing written
    for it. **Next session: start there, by asking that same question
    again** (don't guess).
  - Verified brace balance after every edit this session (final count:
    usa.txt 14,647/14,647, new_usa_ideas.txt 1,588/1,588 — matched, no
    breakage).

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
- **Crisis of Dependency tree is IN PROGRESS, not done** — currently mid
  rework, focus-by-focus, collaboratively with the user (see the
  2026-09-02 "cont. 3" session log entry above for the full design
  framework this is now following: dynamic-modifier variable value tiers,
  no weekly modifiers, multi-family stacking with real trade-offs, timed
  ideas, cost 5). Status per focus, in tree order:
  - `USA_crisis_of_dependency`, `USA_the_bay_otzen_plan` — **user-authored**,
    finished, these are the style template.
  - `USA_invest_in_infrastructure`, `USA_reinforce_interstate_routes` —
    done to the new standard this session.
  - Everything else (`USA_finance_new_mines_and_refineries` onward through
    the capstone `USA_north_american_autarky`, 17 focuses) is still at the
    **old flat-rescale numbers** from the tally-based pass earlier in this
    same session — mechanically complete and in-range on raw totals, but
    NOT yet reworked to the tiered-value/multi-family/trade-off standard.
    Don't treat these as finished; don't build further on top of them
    without redoing them first.
  - Process: ask the user for that focus's goal/tone before writing it,
    one focus at a time — don't batch-design the rest blind, that's what
    produced the underpowered first draft. Next up: `USA_finance_new_mines_and_refineries`.
  - Still needed once all 21 are reworked: real flavor text for the
    `_desc` keys (currently `TO-COMPLETE` placeholders in
    `new_usa_focus_l_english.yml`) and in-game testing — not yet verified
    live.
- `USA_kickstart_the_steel_belt` (the sibling AN industry tree, "Heavy
  Industry" section right after Crisis of Dependency in `usa.txt`, now
  starting ~line 36653) is still a full stub — empty `completion_reward`
  throughout. It's themed around army/navy funding (children include
  `USA_focus_on_our_fleets`, `USA_increase_naval_funding`,
  `USA_focus_on_our_army`, etc.), so if it follows the same FREE-tie-in
  design as Crisis of Dependency, it likely wants `mil`/`arm`/`nav` category
  ticks rather than `civ` — confirm with the user before assuming. When this
  tree gets its pass, reuse the tally-and-scale approach from the Crisis
  rescale (see `tally.py` in that session's scratchpad, or rewrite it) rather
  than eyeballing magnitudes — the same "well under 1/10 the weight" problem
  is likely to recur otherwise.

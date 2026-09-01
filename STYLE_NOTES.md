# Style Notes — Anozin's USA Reimagined

Read this alongside CLAUDE.md before writing any new content. CLAUDE.md sets
the *rules* (confirm files first, match nearest example, don't refactor);
this file records the *patterns* those rules point at, gathered from a
read-through of the codebase on 2026-08-31. When in doubt, still go re-read
the nearest existing example of what you're writing — this file is a map,
not a substitute for the source.

## Focus tree (common/national_focus/usa.txt)

- Constants live at the top of the file as `@name = value # comment`,
  grouped under `#####` divider blocks, one block per mechanic/timer
  family (e.g. "De-Intervention Timers", "New Deal Timers"). Old values are
  kept as inline comments ("# used to be 30") instead of being deleted —
  preserve that trail when retuning a number.
- Every `focus = { ... }` block's opening brace gets a trailing
  `# Human-Readable Name` comment, in addition to the `id`.
- Standard fields, roughly in this order: `id`, `icon`, `overlay`,
  `mutually_exclusive`/`prerequisite`, `x`/`y`/`relative_position_id`,
  `cost`, `ai_will_do`, `allow_branch` (if any), `search_filters`,
  `available`, `bypass`, then `completion_reward`. Follow this order for
  new focuses rather than inventing a different field sequence.
- `hidden_effect = { ... }` wraps anything that shouldn't show in the
  tooltip (flag-setting, dynamic modifier swaps, `mark_focus_tree_layout_dirty`).
- `custom_override_tooltip` and `custom_effect_tooltip` are used constantly
  to collapse multi-effect focuses into one clean tooltip line — this is a
  core idiom, not an occasional trick. New focuses with several small
  effects should get a matching tooltip override, following the nearest
  existing example's localization key naming.
- `skip_line_tt = yes` is sprinkled between effects to control tooltip
  spacing — match its placement in a nearby focus rather than guessing.
- Path-locking/branch-hiding uses
  `allow_branch = { if = { limit = { has_game_rule = { rule = obsolete_focus_branches_visibility option = HIDE } } <path check> } }`
  plus a `hidden_effect = { mark_focus_tree_layout_dirty = yes }` guarded the
  same way. This is a deliberate accessibility feature (old/unchosen
  branches visually collapse) — preserve it on new branch-locking focuses,
  don't simplify it away.
- Dead or draft code is left in place commented out with `#` (old tooltip
  drafts, unused stealth/side-message focuses tagged `[unused]`). Treat
  these as intentional scratch history. Don't delete commented blocks
  you didn't add unless asked.
- Section dividers use nested `#`-line blocks of increasing indent for
  hierarchy (e.g. `####` top-level section, `  ####` sub-path, matching the
  code's own indent level at that point) — match the divider weight to the
  nesting depth of the surrounding tree, not a flat style.

## The FREE mechanic (spans several files — read all of them before touching it)

FREE is not a normal linear dynamic modifier. It's a hand-built points +
tier-ladder system. The chain, in order:

1. A focus calls a scripted effect like `USA_AN_FREE_civ_up_low`
   (defined in [common/scripted_effects/new_usa_scripted_effects.txt](common/scripted_effects/new_usa_scripted_effects.txt)).
2. That effect checks `has_dynamic_modifier = USA_FREE_dynamic_modifier`
   (or `_2`), then `add_to_variable`/`subtract_from_variable`s fixed amounts
   (`FREE_points_A`, `FREE_points_V`, etc. — scripted constants, not magic
   numbers written inline) into a running score variable per category
   (e.g. `USA_FREE_civ_points`).
3. It calls `USA_AN_check_FREE_all`, which re-resolves the score into the
   active tier.
4. The tier itself is one of seven `hidden_ideas` per category, named
   `usa_free_<category>_si_-3` through `_si_3` (civ, mil, arm, nav, air,
   sci — negative = penalty tiers, positive = bonus tiers), defined in
   [common/ideas/new_usa_ideas.txt](common/ideas/new_usa_ideas.txt). Every
   tier idea is `allowed = { always = no }` with `removal_cost = -1`,
   which is what lets the game silently swap the active one under script
   control instead of the player picking it.
5. Separately, several `dynamic_modifiers` (in
   [common/dynamic_modifiers/new_USA_dynamic_modifiers.txt](common/dynamic_modifiers/new_USA_dynamic_modifiers.txt))
   point modifier fields at *named country variables*
   (`political_power_cost = USA_eco_dy_political_power_cost`) rather than
   literal numbers, and focuses adjust those variables via `add_to_variable`.
   This is the same underlying trick reused for the non-FREE economy path
   (Great Depression recovery spirits) — expect to see it again anywhere a
   modifier needs to scale off player choices rather than time.
6. `USA_FREE_CHECK` (in the focus tree) is a hidden debug focus
   (`ai_will_do = { factor = 0 }`, `allow_branch = { always = no }`) that
   fires every tier-up effect at once, purely for manual testing. Don't
   remove it; it's a dev tool, not dead content.

New FREE sub-tree focuses (the center/right/left magnitude-and-shape
branches mentioned in CLAUDE.md) should hook into this exact chain —
new scripted effects following the `USA_AN_FREE_<cat>_up_<tier>` naming,
new points constants if needed, no parallel scaling system.

## Events (events/USA_new_events.txt)

- `add_namespace = <name>` precedes each logical group of events; ids are
  sequential within it (`name.1`, `name.2`, ...).
- Backend/auto-focus event chains (things that fire from focus completion
  rather than player choice) are `hidden = yes`, `is_triggered_only = yes`,
  and daisy-chained with
  `set_country_flag = { flag = x days = @timer value = 1 }` followed by a
  second `country_event = { id = next days = @timer }` rather than a
  polling trigger. Timer values are the same `@name` constants declared at
  the top of the file (a parallel set to the focus-tree ones — check both
  files' constant blocks when adding a new timed chain).

## Decisions (common/decisions/new_USA.txt)

- War-plan-style decisions pair a flat `modifier` with a `targeted_modifier`
  against a specific `tag`, plus `fire_only_once = yes` and
  `days_remove = N` so the bonus self-expires. Use this pattern for any
  "declare an intent, get a temporary situational bonus" decision.
- `visible` typically gates on `has_completed_focus` plus a `NOT` for a
  later focus that supersedes it — check the nearest sibling decision's
  gating before writing a new one.

## Scripted triggers/effects/localisation

- `common/scripted_triggers/usa_mod_scripted_triggers.txt` wraps repeated
  `check_variable` conditions behind `custom_trigger_tooltip` so the
  player sees a clean localized condition instead of raw variable math.
- Indentation is **not** perfectly uniform mod-wide: focus/event/idea files
  are tab-indented; some scripted_triggers entries use 4-space blocks
  instead. Match whichever file you're actually editing — don't impose one
  global indent style across files.

## Localisation (localisation/english/*.yml)

- Only the `english` locale is current; `russian` and `simp_chinese` lag
  behind and shouldn't be treated as a style reference or kept in sync
  automatically.
- Two live key conventions coexist in the same focus localisation file:
  older/simple entries use bare `KEY` (title) + `KEY_desc`; newer
  "modular" sections use `KEY-general_title` / `KEY-general_desc`. Match
  whichever convention borders the focus you're adding — don't normalize
  the whole file to one style.
- Flavor text is first-person-plural, in-character ("We must...", "Our
  enemy will..."), leans on real historical references reworked into
  alt-history beats (New Deal, Glass-Steagall), and isn't afraid of
  anachronistic humor when it fits the joke focus (e.g. `USA_doge`).
- `custom_effect_tooltip`/`custom_override_tooltip` keys used by focuses
  live in the same localisation files as the focus text itself — search
  for the focus's id first to find its whole loc block in one place.

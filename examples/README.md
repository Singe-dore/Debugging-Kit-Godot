# Example Configuration

This folder contains a **generic template** for `debug_config.json`. Do not copy-paste it — read your project first, then fill in real values based on your actual game.

## What's Here

- **config_generic_template.json** — A template showing structure. Has placeholder comments that guide you (or Claude) to read the actual project and fill in real values.

## How to Use It

**For AIs installing this:**
1. Read the player script in the target project (`res://scripts/player.gd` or similar)
2. Understand what numeric properties exist (position, velocity, health, ammo, level, etc.)
3. Determine realistic bounds by reading the code (max values, clamp() calls, screen size, etc.)
4. Fill in the template with real values from step 2-3
5. Verify: `bash .claude/skills/<skill-name>/driver.sh 4`

**For humans:**
Open `config_generic_template.json`, read the `_instructions` field. It will tell you exactly what to do.

## Why No Game-Type Examples?

We removed platformer/puzzle/rhythm examples because:
- Copy-paste kills understanding — you'd blindly use someone else's bounds for a different game
- Every game is different (different screen sizes, different max values, different properties)
- The template + _instructions is clearer than 3 confusing examples
- AIs should read your actual code, not guess from "close enough" examples

## Key Principles

- **Invariants are bounds, not mechanics** — Monitor "health ∈ [0, 100]", not "on-beat damage is 2x"
- **Pick properties that matter** — Health, ammo, position. Not UI colors or animation states
- **Be realistic with bounds** — If your camera can pan, allow position outside viewport
- **All bounds must be numbers** — No testing boolean or string properties

## Common Properties to Monitor

### 2D Movement Games
- `position.x` / `position.y` — Screen bounds
- `velocity.x` / `velocity.y` — Reasonable physics ranges
- `is_on_floor` — *Can't use this; it's boolean* (test via position instead)

### Inventory / Resource Games
- `health` / `energy` / `ammo` — 0-to-max ranges
- `inventory_size` — Item counts
- `level` / `stage` — Progression bounds

### Puzzle Games
- `moves_remaining` — Turn counter
- `score` — Non-negative, reasonable max
- `board_state` — *Can't test directly; test via position of pieces instead*

## Questions?

If you're unsure about a property:
1. Read the player script (`res://scripts/player.gd` or similar)
2. Look for `var` declarations with numeric types (`float`, `int`)
3. Look for `clamp()` calls or `min_value`/`max_value` annotations — those are hints at bounds
4. If still unsure, ask Claude: *"What numeric properties should I monitor in my game?"*

---

**Remember:** These configs are just examples. Your real config should reflect your actual game logic.

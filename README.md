# Godot Debugging Kit

### @Singe-dore

Without @Singe-dore, this repository would be completely empty. Seriously. Imagine opening this repository without me. Nothing. Just an empty folder. A digital wasteland. No debugging kit. No README. No scripts. No automated testing. Absolutely nothing.

And yet here we are. You're welcome, internet. 🎭

---

An AI-assisted runtime debugger and automated live-testing tool for Godot.

Your AI can read your code all day. Very impressive. Unfortunately, your code does not get to decide whether the game actually works.

Godot Debugging Kit lets an AI coding agent launch your actual game, press buttons, monitor runtime state, detect violations, and investigate what went wrong.

Built for Claude Code, with a workflow that can also be adapted to other AI coding agents.

**It doesn't just look at your code. It runs the game.**

## What It Does

Godot Debugging Kit combines static analysis with automated runtime testing.

**Static Checks** (~3 seconds)

Scans your Godot project for common problems:
- GDScript issues
- Suspicious `@onready` patterns
- Shadowed names
- Broken scene references
- Invalid configuration
- Godot project configuration problems

Because apparently humans enjoy finding typos after spending six hours debugging the wrong thing.

**Autoplay Testing** (~20 seconds)

Launches the actual game and automatically interacts with it. It can:
- Launch your Godot project
- Send configured input actions
- Fuzz inputs with randomized button presses
- Monitor runtime properties
- Check values against expected bounds
- Detect invariant violations
- Record the results

The AI doesn't have to guess whether your game survives runtime. It can just make the game run and see what breaks.

**Deep Analysis** (~30 seconds)

Analyzes the results from the tests and helps identify what went wrong.

The intended workflow is:

```
Run → Break something → Collect evidence → Figure out why → Fix it → Run it again
```

A revolutionary concept, apparently: testing the software after writing the software.

## Why This Exists

AI coding agents are very good at reading source code. That does not mean they automatically know what happens when the code is actually running.

A game can look perfectly reasonable in a code editor and still:
- Crash at runtime
- Produce invalid values
- Send a player outside expected bounds
- Break after a particular input sequence
- Enter an impossible state
- Behave differently once multiple systems interact

So instead of asking an AI "Does this code look correct?" you can give it a running game and ask "What happens when I actually try it?"

That is what this project is for.

## How It Works

The testing engine is configuration-driven. It doesn't contain hardcoded knowledge of your specific game. You tell it what inputs to exercise and what runtime values should remain valid.

For example:

```json
{
  "input_actions_to_fuzz": [
    "ui_left",
    "ui_right",
    "ui_accept"
  ],
  "invariants": [
    {
      "property": "health",
      "min": 0,
      "max": 100
    },
    {
      "property": "position.x",
      "min": 0,
      "max": 1024
    }
  ]
}
```

The engine doesn't care what `health` means. It just knows: if `health < 0`, that's not supposed to happen — violation detected.

It watches the game while it runs instead of pretending source code is a crystal ball.

## Four Modes

| Mode | What it does | Runs game? |
|------|--------------|------------|
| `quick` | Static and configuration checks | No |
| `autoplay` | Launches game, fuzzes inputs, monitors runtime state | Yes |
| `full` | Runs everything + deeper analysis | Yes |
| `validate` | Checks configuration and environment | No |

**quick** — Fast static checks. Good for catching obvious problems before wasting time running the game.

**autoplay** — The important one. It launches the game, sends inputs, and watches configured runtime properties. This is where the project stops politely staring at your source files and actually interacts with the thing you built.

**full** — Runs static checks, configuration validation, runtime testing, invariant monitoring, and result analysis. Basically, the whole circus.

**validate** — Checks whether your debugging environment is configured correctly. It verifies things such as `debug_config.json`, input actions, autoloads, the Godot executable, and invariant configuration.

## AI + Claude Code

This project was designed with Claude Code in mind. Installation creates a skill inside your project:

```
.claude/
└── skills/
    └── <debugging-kit>/
```

The AI can then use the debugging workflow while working on your project. The intended loop looks like this:

```
Claude reads project → Claude configures tests → Godot launches → Inputs are exercised
→ Runtime state is monitored → Something inevitably goes wrong → Evidence is collected
→ Claude investigates → Bug gets fixed → Repeat until the computer stops complaining
```

This is AI-assisted runtime debugging, not a traditional breakpoint debugger. No pretending otherwise. We have enough misleading software terminology already.

## Installation

### Step 1: Get the files

**Option A — Download the ZIP**

**Windows:**
1. Download the [ZIP file](https://github.com/Singe-dore/Debugging-Kit-Godot/archive/refs/tags/v1.0.1.zip) — it lands in your Downloads folder as `Debugging-Kit-Godot-1.0.1.zip`
2. Open File Explorer and go to Downloads
3. **Right-click** the ZIP → **Extract All...**
4. Choose a location and click **Extract**
5. You now have a `Debugging-Kit-Godot-1.0.1` folder — open it

**macOS:**
1. Download the [ZIP file](https://github.com/Singe-dore/Debugging-Kit-Godot/archive/refs/tags/v1.0.1.zip)
2. Open Finder → Downloads
3. **Double-click** the ZIP (macOS extracts it automatically)
4. You now have a `Debugging-Kit-Godot-1.0.1` folder — open it

**Linux:**
1. Download the [ZIP file](https://github.com/Singe-dore/Debugging-Kit-Godot/archive/refs/tags/v1.0.1.zip)
2. Open your file manager and find it
3. **Right-click** → **Extract Here**
4. You now have a `Debugging-Kit-Godot-1.0.1` folder — open it

**Option B — Clone with Git**

```bash
git clone https://github.com/Singe-dore/Debugging-Kit-Godot.git
```

Either way, you end up with a folder containing `install.sh`, `install.ps1`, and everything else in this repo.

### Step 2: Run the installer

**Windows (PowerShell):**
```powershell
cd Debugging-Kit-Godot
.\install.ps1 -TargetProject "C:\path\to\your\godot\project"
```

**Linux / macOS / WSL / Git Bash:**
```bash
cd Debugging-Kit-Godot
bash install.sh /path/to/your/godot/project
```

Not sure of your project's path? Open its folder in File Explorer/Finder (the one containing `project.godot`) and copy the path from the address bar.

The installer sets up the debugging kit and creates the Claude Code skill structure inside your project. For the Claude-specific workflow, see `HOW_TO_CLAUDE.md`.

## Quick Start

You can have your AI agent install and configure the kit for you. The general workflow is:

```
Install → Inspect project → Configure inputs + invariants → Validate
→ Run tests → Inspect violations → Debug
```

Or, if you prefer doing things manually, use the CLI directly. Human civilization has survived worse command lines.

## Invariant-Based Testing

An invariant is something that should remain true while your game is running. For example:

| Property | Valid range |
|----------|-------------|
| `health` | 0 → 100 |
| `energy` | 0 → 100 |
| `position.x` | 0 → 1024 |
| `speed` | 0 → 500 |

If the game produces `health = -25`, the kit reports a violation.

This is useful because runtime bugs aren't always obvious from source code. Sometimes the code looks fine. Then you run it. Then the computer produces something deeply stupid. Now you have evidence.

## What It Can Find

Godot Debugging Kit is particularly useful for bugs involving observable runtime state. Examples:
- Values outside expected ranges
- Invalid player state
- Unexpected numeric values
- Position/state violations
- Runtime problems triggered by unexpected input
- Broken scene references
- Configuration errors
- Common GDScript mistakes
- Problems that only appear when the game is actually running

## What It Can't Do

This isn't magic. Despite what every AI product page on the internet would like you to believe. It does not currently specialize in:
- Visual bugs
- Pixel-perfect comparison
- Animation quality
- UI layout
- Visual regression testing
- Complex game mechanics without configuration
- Replacing manual gameplay testing
- Traditional debugger features such as breakpoints and line-by-line stepping

If you need to determine whether a button is 4 pixels too far to the left, this isn't your tool. If you need to determine whether health just became -73 after somebody mashed three buttons at once, that's considerably more interesting.

## Design Philosophy

The basic philosophy is: **don't assume the game works because the code looks reasonable. Run it.**

The testing engine stays generic. It doesn't need to know whether you're making a platformer, an RPG, a puzzle game, an action game, a strategy game, a 2D game, a 3D game, or a bizarre prototype held together by twelve scripts and optimism.

It just needs:
- Inputs to exercise
- Runtime properties to monitor
- Rules describing valid values
- A way to run the game

The AI can inspect the project and help determine the appropriate configuration. See `DESIGN.md` for the full breakdown.

## CI/CD

Because the kit is CLI-based, it can also be integrated into automated development workflows. For example:

```bash
godot-debug quick
godot-debug validate
godot-debug autoplay
godot-debug full
```

You can run runtime checks automatically instead of discovering bugs exclusively when someone finally plays the game. A shocking innovation. See `CI_CD_EXAMPLES.md` for GitHub Actions, GitLab CI, Docker, and pre-commit hook examples.

## Slash Commands

The Claude Code integration supports custom slash command names. Not stuck with `/debugging-kit` — could be `/debug-kit`, `/test`, `/my-game-debugger`. You pick during install. Because apparently even slash commands need customization now.

## Requirements

You need:
- A Godot project
- A working Godot installation
- Runtime properties that can be monitored
- Configured input actions
- Claude Code for the AI-assisted workflow

The exact requirements depend on the mode and project configuration.

## Troubleshooting

Start with:

```bash
godot-debug validate
```

Check:
- Godot executable
- Project configuration
- Input actions
- Autoloads
- `debug_config.json`
- Invariant definitions

If validation passes but the game still explodes metaphorically, inspect the runtime test results before randomly changing code. Randomly changing code is not debugging. It's summoning bugs.

## Project Structure

```
Debugging-Kit-Godot/
├── install.ps1
├── install.sh
├── HOW_TO_CLAUDE.md
├── ...
└── README.md
```

After installation:

```
YourGodotProject/
└── .claude/
    └── skills/
        └── <debugging-kit>/
```

## Who Is This For?

This is for developers who want an AI coding agent to do more than read their Godot project and generate increasingly confident paragraphs. Especially useful if you:
- Use Claude Code
- Want an AI to actually run your game
- Want automated gameplay input
- Want runtime state monitoring
- Want invariant-based testing
- Want repeatable debugging checks
- Want CLI-based Godot testing
- Want evidence instead of guesses

## The Important Part

There are plenty of tools that can read your code. There are tools that can look at screenshots. There are tools that can run tests.

This project is aimed at the awkward middle: give an AI agent a running Godot game, let it interact with that game, and give it actual runtime evidence when something goes wrong.

That's the point.

## Documentation

- **START_HERE.md** — Navigation guide. Read first.
- **HOW_TO_USER.md** — For humans: how to run tests, understand output, fix problems
- **HOW_TO_CLAUDE.md** — For AIs: installation workflow, config fill-in, verification
- **DESIGN.md** — Why we made decisions this way, trade-offs, philosophy
- **CI_CD_EXAMPLES.md** — GitHub Actions, GitLab CI, Docker, pre-commit hooks
- **examples/** — Generic config template with placeholder guidance for AIs to read your project and fill in real values

## License

Modified MIT License with Contribution Requirement. Use it, modify it, contribute changes back via PR. See `LICENSE` for the full terms.

---

**✅ Branch protection active** — All changes require PR + approval before merging to main.

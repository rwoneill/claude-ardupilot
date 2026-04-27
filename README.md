# claude-ardupilot

Drop-in templates for using Claude as an ArduPilot assistant — for firmware questions, Lua scripts, parameter lookups, and dataflash log analysis.

## What's in this repo

| File | Purpose |
| --- | --- |
| [`CLAUDE.md`](CLAUDE.md) | Project-level context file. Place at the root of a Claude Code project folder. |
| [`SKILL.md`](SKILL.md) | Skill file for Claude.ai. Upload to your Skills section so it auto-activates on ArduPilot questions across any conversation. |
| [`.claude/settings.local.json`](.claude/settings.local.json) | Pre-approved permissions for common ArduPilot workflows (WebFetch, pymavlink, read-only git). Used by Claude Code only. |

## Setup walkthrough

The full step-by-step setup guide for new users — including what subscription you need, how to install Claude Desktop, and how to start a project session — lives on the OI Engineering Notion:

**→ [Claude Code Environment Setup for ArduPilot](https://www.notion.so/34ee5ade2e3781f38a92fc18fae01111)**

## Quickstart by tier

### Claude Code (Pro/Max plan with Code feature)

1. Make a project folder.
2. Download `CLAUDE.md` into the root, and `settings.local.json` into a `.claude/` subfolder.
3. Start a Claude Desktop code session pointed at the folder.
4. Paste the setup prompt from the Notion guide.

### Skills (any paid Claude.ai plan, also API)

1. Download `SKILL.md`.
2. Optionally fill in the **CONFIGURE ME** block at the top with your aircraft details.
3. Upload to Claude.ai under the **Skills** section.
4. Ask any ArduPilot question in any conversation — Claude pulls the skill in automatically.

### Projects (paid Claude.ai plans)

1. Download `CLAUDE.md`.
2. Create a Claude Project and attach `CLAUDE.md` as project knowledge.
3. Have ArduPilot conversations inside that Project.

### Paste-at-start (free tier works)

1. Download `CLAUDE.md`.
2. Paste its contents at the start of any new chat. Claude uses it as context for that conversation.

## What this gives you

When the context is loaded, Claude:

- **Cites source files** (e.g. `libraries/AP_Motors/AP_MotorsMatrix.cpp:142`) when explaining ArduPilot behavior.
- **Verifies against the matching firmware branch** before answering anything version-specific. No made-up parameter names or Lua bindings.
- **Uses `libraries/AP_Logger/LogStructure.h`** as the source of truth for dataflash log fields — not third-party parsers that may be out of date.
- **Flags uncertainty** instead of guessing.

Works for any vehicle (Plane, Copter, Rover, Sub, Blimp, AntennaTracker) and any release branch.

## Updating

When ArduPilot ships a new release, just ask Claude to update your local copy of `CLAUDE.md` (or your uploaded `SKILL.md`) to target the new branch. Or re-pull this repo for any template improvements.

## License

MIT.

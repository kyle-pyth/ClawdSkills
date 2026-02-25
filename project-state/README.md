# 🧠 Project State

> Never lose context again. Track projects, decisions, and blockers for seamless AI session recovery.

## The Problem

AI assistants lose context between sessions. You end up re-explaining where you left off, what was decided, and what's blocking progress. Projects stall. Time is wasted.

## The Solution

A lightweight system that:

- 📍 **Tracks where you are** in each project
- 📝 **Records decisions** and why you made them
- 🚧 **Flags blockers** and open questions
- ⚡ **Resumes instantly** with a "resume prompt"
- 💾 **Auto-saves** at natural checkpoints

## How It Works

| File | Purpose | When Loaded |
|------|---------|-------------|
| `ACTIVE.md` | Hot projects list | Every session start |
| `STATE.md` | Per-project status | When project mentioned |
| `REGISTRY.md` | All projects index | On demand |

Your AI reads `ACTIVE.md` at session start (tiny, fast). When you mention a project, it loads that `STATE.md`. Progress saves automatically at checkpoints.

## Quick Start

### 1. Install

**Via ClawHub:**
```bash
clawhub install project-state
```

**Via Git:**
```bash
git clone https://github.com/YOUR_USERNAME/project-state.git skills/project-state
```

**Manual:** Copy the `skills/project-state/` folder to your workspace.

### 2. Setup

Say "setup project state" to your AI, or follow the onboarding in `ONBOARDING.md`.

The skill will:
1. Scan your workspace structure
2. Ask 3 quick config questions
3. Create the state tracking files
4. Optionally import existing projects

### 3. Use

| Command | Action |
|---------|--------|
| `project status` | See all active projects |
| `new project <name>` | Start tracking a project |
| `save state` | Checkpoint current work |
| `resume <project>` | Load context for a project |
| `close project` | Archive when done |

## File Structure

After setup, your workspace will have:

```
your-workspace/
├── state/
│   ├── ACTIVE.md       # Hot projects (loaded every session)
│   ├── REGISTRY.md     # All projects index
│   └── config.json     # Your preferences
└── projects/
    └── my-project/
        └── STATE.md    # Per-project state
```

## Configuration

The skill auto-detects your workspace type and suggests optimal defaults:

- **Clawdbot workspaces:** State files live with projects
- **Monorepos:** Separate state folder
- **Simple workspaces:** Self-contained `.project-state/` folder

Edit `state/config.json` to customize paths, auto-save triggers, and more.

## Security

- **Path safety:** All operations validated to stay within workspace
- **Scan safety:** Only reads directory names, not file contents
- **Exclusions:** Skips `.env`, `*.key`, `secrets/`, etc.
- **Rate limiting:** Auto-saves throttled to prevent spam
- **Size limits:** State files capped to prevent unbounded growth

See `SKILL.md` for full security documentation.

## Example STATE.md

```markdown
# Project: My App

**Status:** 🟢 Active
**Phase:** 3/5 - Building MVP

## Resume Prompt
> Backend API complete. Working on frontend auth flow. 
> Next: implement OAuth callback handler. Blocked on 
> getting Google OAuth credentials from client.

## Open Questions
- [ ] Waiting on OAuth credentials
- [ ] Confirm error handling approach

## Decisions
| Date | Decision | Choice | Rationale |
|------|----------|--------|-----------|
| 02-25 | Auth method | OAuth | Client requirement |
| 02-24 | Framework | Next.js | Team familiarity |
```

## Compatible With

- [Clawdbot](https://github.com/clawdbot/clawdbot) / OpenClaw
- Any AI assistant that can read markdown files
- Works standalone for human project tracking too

## License

MIT — use it however you want.

## Contributing

Issues and PRs welcome! This skill was built to solve a real problem — if you have ideas to make it better, let's hear them.

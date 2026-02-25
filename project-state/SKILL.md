# Project State Skill

> Never lose context again. Track projects, decisions, and blockers for seamless session recovery.

## Overview

AI assistants lose context between sessions. This skill creates a lightweight system to:

- 📍 Track where you are in each project
- 📝 Record decisions and why you made them
- 🚧 Flag blockers and open questions
- ⚡ Resume instantly with a "resume prompt"
- 💾 Auto-save at natural checkpoints

## Core Files

| File | Purpose | When Loaded |
|------|---------|-------------|
| `ACTIVE.md` | Hot projects list with status | Every session start |
| `REGISTRY.md` | All projects index | On demand ("project status") |
| `STATE.md` | Per-project detailed state | When project mentioned |
| `DECISIONS.md` | Decision log with rationale | Optional, with STATE.md |

## Installation

1. Copy `skills/project-state/` to your skills folder
2. Run onboarding: "setup project state" or read ONBOARDING.md
3. The skill will scan your workspace and configure automatically

## Session Protocol

### On Session Start (Mandatory)

```
1. Read state/ACTIVE.md (or configured location)
2. Note any blockers or items needing attention
3. Mention hot projects briefly if relevant
```

### During Session

```
1. Detect project references → load that project's STATE.md
2. On decisions → append to STATE.md decisions section
3. On blockers identified → add to open questions
4. On manual "save state" → full checkpoint
```

### On Project Pause/End

```
1. Update STATE.md with current position
2. Write clear "resume prompt" 
3. Update ACTIVE.md status
4. Note any blockers
```

## Auto-Save Triggers

| Trigger | What's Saved |
|---------|--------------|
| Phase/milestone complete | Update phase, write summary |
| Decision made | Append to decisions table |
| Blocker identified | Add to open questions, flag in ACTIVE |
| Project paused | Full state capture, resume prompt |
| Manual "save state" | Full checkpoint |
| Project started | Create STATE.md, add to ACTIVE + REGISTRY |

### Trigger Detection

| User Says | Trigger |
|-----------|---------|
| "let's go with X", "decided", "confirmed" | Decision |
| "need from you", "waiting on", "blocked" | Blocker |
| "pause", "come back to", "hold off", "table this" | Pause |
| "new project", "let's build", "spec out" | New project |
| "save state", "checkpoint", "save progress" | Manual save |
| "done", "shipped", "complete", "finished" | Project close |

## Commands

| Command | Action |
|---------|--------|
| `project status` | Show ACTIVE.md contents |
| `new project <name>` | Create STATE.md from template, add to ACTIVE + REGISTRY |
| `save state` / `checkpoint` | Capture current project state |
| `resume <project>` | Load STATE.md, summarize current position |
| `close project <name>` | Mark complete, remove from ACTIVE, keep in REGISTRY |
| `project history` | Show REGISTRY.md (all projects) |

## File Formats

### ACTIVE.md

```markdown
# Active Projects
> Last updated: 2025-02-25 22:17 UTC

| Project | Status | Phase | Next Action | Blocker |
|---------|--------|-------|-------------|---------|
| market-pulse | 🟡 Paused | Spec done | Awaiting setup | Yes |
| c4-automation | 🟢 Active | Building | Finish scraper | No |

## Quick Context
- **market-pulse**: News channel using PythPriceBot for charts. Waiting on bot choice.
```

### STATE.md

```markdown
# Project: {name}
**Status:** 🟢 Active | 🟡 Paused | 🔴 Blocked | ✅ Done
**Created:** YYYY-MM-DD
**Updated:** YYYY-MM-DD
**Phase:** X/Y - Phase Name

## Resume Prompt
> One paragraph that tells future-you exactly where you left off.
> Include: current state, recent decisions, immediate next step.

## Phases
- [x] 1. Phase one
- [ ] 2. Current phase
- [ ] 3. Future phase

## Open Questions
- [ ] Question or blocker 1
- [ ] Question or blocker 2

## Decisions
| Date | Decision | Choice | Rationale |
|------|----------|--------|-----------|
| MM-DD | What was decided | What was chosen | Why |

## Context Links
- Related file: path/to/file
- External: https://...
```

### REGISTRY.md

```markdown
# Project Registry

| Project | Status | Category | Created | Last Active |
|---------|--------|----------|---------|-------------|
| market-pulse | 🟡 Paused | product | 2025-02-24 | 2025-02-25 |
| c4-automation | 🟢 Active | automation | 2025-02-20 | 2025-02-25 |
| competitors | 📚 Ongoing | research | 2025-01-15 | 2025-02-20 |

## Status Legend
- 🟢 Active — Currently being worked on
- 🟡 Paused — Temporarily on hold
- 🔴 Blocked — Waiting on external input
- ✅ Done — Completed
- 📚 Ongoing — Continuous/maintenance work
- 😴 Dormant — Inactive, may revisit

## Keywords
Map keywords to projects for smart detection:
- market pulse, news channel, pythmarketpulse → market-pulse
- c4, code4rena, audits → c4-automation
```

## Configuration

Config file: `state/config.json` (or `.project-state/config.json`)

```json
{
  "workspaceType": "clawdbot",
  "stateDir": "./state",
  "projectsDir": "./projects",
  "stateLocation": "with-projects",
  "stateFileName": "STATE.md",
  "activeFile": "./state/ACTIVE.md",
  "registryFile": "./state/REGISTRY.md",
  "autoSave": {
    "onCheckpoint": true,
    "onPause": true,
    "onManual": true,
    "onDecision": true
  },
  "loadActiveOnStart": true
}
```

### stateLocation Options

- `"with-projects"` — STATE.md lives in each project folder: `projects/foo/STATE.md`
- `"separate"` — All state in central folder: `state/projects/foo/STATE.md`

## Best Practices

### Writing Good Resume Prompts

❌ Bad: "Working on the bot"
✅ Good: "Architecture confirmed: separate news bot pings PythPriceBot for charts, forwards to @PythMarketPulse. Channel exists. Waiting on: which bot to use, watchlist confirmation, soft launch decision."

### Keeping ACTIVE.md Useful

- Max 5-7 active projects (if more, some should be paused)
- Update "Quick Context" with latest state, not history
- Clear blockers promptly

### Decision Hygiene

- Record decisions when made, not later
- Include rationale — future-you will thank you
- Link to relevant context (messages, docs)

## Security Considerations

### Path Safety (CRITICAL)

All file operations MUST validate paths stay within workspace:

```
1. Resolve path to absolute
2. Verify starts with workspace root
3. Reject if contains ".." after resolution
4. Reject absolute paths from user input
```

**Never allow:** `../../../etc/passwd`, `/etc/cron.d/malicious`, symlinks outside workspace

### Workspace Scan Safety

During onboarding, the scan:
- Reads directory names and file extensions ONLY (not contents)
- Maximum depth: 3 levels
- Skips sensitive patterns:
  - `.env*`, `*.key`, `*.pem`, `*credentials*`, `*secret*`
  - `.git/`, `.clawdbot/`, `node_modules/`, `__pycache__/`
  - `secrets/`, `.ssh/`, `.config/`
- Times out after 10 seconds
- Shows user what will be scanned BEFORE scanning

### Data Privacy

**State files may contain project details — treat as internal docs.**

**Never store in state files:**
- Passwords, API keys, tokens
- Private keys, credentials
- Personal identifiers (unless project-relevant)
- Full file paths to sensitive files

**Recommendation:** Add `.gitignore` entry for `state/` if workspace is public.

### Rate Limiting

Auto-saves are rate-limited:
- Minimum 60 seconds between auto-saves
- Debounce rapid triggers
- Manual "save state" bypasses rate limit

### File Size Limits

Prevent unbounded growth:
- Max state file size: 100KB
- Max entries per section: 50 decisions, 50 blockers
- Archive old state when limits reached

## Troubleshooting

**State not loading?**
- Check config.json paths are correct
- Verify ACTIVE.md exists at configured location

**Auto-save not triggering?**
- Triggers are heuristic — use explicit "save state" if needed
- Check autoSave settings in config

**Too much noise?**
- Reduce auto-save triggers in config
- Use "with-projects" stateLocation to keep state near code

---

*See ONBOARDING.md for setup instructions.*

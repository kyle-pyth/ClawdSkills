# Project State — Onboarding Flow

This document describes the interactive onboarding process for setting up the project-state skill.

## Trigger

Onboarding runs when:
- User says "setup project state" or "install project state"
- Skill is installed and no config exists
- User says "reconfigure project state"

---

## Phase 0: Introduction

**Goal:** Explain value before asking for anything.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 PROJECT STATE — Never Lose Context Again
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Hey! I'm going to help you set up project tracking so you 
(or your AI assistant) never loses context mid-project.

THE PROBLEM
───────────
AI assistants lose context between sessions. You end up 
re-explaining where you left off, what was decided, and 
what's blocking progress. Projects stall. Time is wasted.

THE SOLUTION
────────────
This skill creates a lightweight system to:

  📍 Track where you are in each project
  📝 Record decisions and why you made them
  🚧 Flag blockers and open questions  
  ⚡ Resume instantly with a "resume prompt"
  💾 Auto-save at natural checkpoints

HOW IT WORKS
────────────
• ACTIVE.md     → Glanceable list of hot projects
• STATE.md      → Per-project status (loaded on demand)
• DECISIONS.md  → What you chose and why (optional)

Your AI reads ACTIVE.md at session start (tiny, fast).
When you mention a project, it loads that STATE.md.
Progress is saved automatically at checkpoints.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ready to set this up? Takes about 2 minutes.

[Continue] [Learn more] [Skip for now]
```

---

## Phase 1: Discovery

**Goal:** Scan workspace, understand existing structure.

### Actions

1. Scan workspace root for known patterns
2. Detect workspace type
3. Find existing project-like folders
4. Report findings to user

### Detection Heuristics

| Pattern | Signal |
|---------|--------|
| `AGENTS.md` + `SOUL.md` | Clawdbot workspace |
| `projects/` with subdirs | Project-based structure |
| `src/`, `packages/`, `apps/` | Monorepo / code-first |
| `docs/`, `notes/` | Documentation-heavy |
| `.git` in multiple subdirs | Multi-repo workspace |
| Flat files only | Simple / new workspace |

### Output

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 LEARNING YOUR WORKSPACE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let me look around and understand your setup...

Scanning...

DETECTED STRUCTURE
──────────────────
📁 Workspace root: {workspace_path}
├── 📄 AGENTS.md        ✓ Clawdbot config
├── 📄 SOUL.md          ✓ Agent identity  
├── 📄 MEMORY.md        ✓ Long-term memory
├── 📂 projects/        ✓ {n} project folders found
├── 📂 memory/          ✓ Daily logs
├── 📂 specs/           ✓ Specifications
└── 📂 skills/          ✓ Agent skills

WORKSPACE TYPE: {detected_type}

I'll optimize the setup for this structure.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Continue]
```

---

## Phase 2: Configuration

**Goal:** Ask minimal questions with smart defaults.

### Questions

Only ask what can't be reliably inferred:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙️  QUICK CONFIGURATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Just 3 quick questions to tailor this to your workflow.

───────────────────────────────────────────────────────
1/3  WHERE DO YOUR PROJECTS LIVE?
───────────────────────────────────────────────────────

I found: {detected_projects_dir}

  [A] Use {detected_projects_dir} ← recommended
  [B] Different location: ___________
  [C] I don't have a projects folder (create one)

Your choice: _

───────────────────────────────────────────────────────
2/3  WHERE SHOULD STATE FILES LIVE?
───────────────────────────────────────────────────────

  [A] With each project ← recommended
      {projects_dir}/my-project/STATE.md
      
  [B] All in one place (separate from projects)
      ./state/projects/my-project/STATE.md

Your choice: _

───────────────────────────────────────────────────────
3/3  WHEN SHOULD I AUTO-SAVE?
───────────────────────────────────────────────────────

  [x] When a milestone/phase completes
  [x] When you say "save state" or "checkpoint"  
  [x] When a project is paused
  [ ] Every hour (can be noisy)

Your choice (or press Enter for defaults): _

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Defaults by Workspace Type

| Type | Projects Dir | State Location | Auto-Save |
|------|--------------|----------------|-----------|
| Clawdbot | `./projects/` | with-projects | checkpoint, pause, manual |
| Monorepo | `./packages/` | separate | checkpoint, manual |
| Simple | `./projects/` (create) | separate | all |
| Custom | ask user | ask user | ask user |

---

## Phase 3: Confirmation

**Goal:** Show what will be created, get approval.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ CONFIRM YOUR SETUP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Here's what I'll create:

STRUCTURE
─────────
{workspace}/
├── state/
│   ├── ACTIVE.md         # Hot projects list
│   ├── REGISTRY.md       # All projects index
│   └── config.json       # Your preferences
└── projects/
    └── {project}/
        └── STATE.md      # Per-project state

BEHAVIOR  
─────────
• Load ACTIVE.md at every session start
• Load STATE.md when project is mentioned
• Auto-save on: {configured_triggers}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Does this look right?

[Yes, create it] [Go back] [Start over]
```

---

## Phase 4: Bootstrap

**Goal:** Create files, optionally import existing projects.

### Actions

1. Create `state/` directory
2. Create `ACTIVE.md` from template
3. Create `REGISTRY.md` from template
4. Write `config.json`
5. Offer to import existing projects

### Import Flow

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📦 IMPORT EXISTING PROJECTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

I found {n} existing projects. Want me to create 
STATE.md templates for them?

  □ project-1           (has README.md)
  □ project-2           (has README.md)
  □ project-3           (no README)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Import all] [Select specific] [Skip - I'll add later]
```

### Import Template

For imported projects, create minimal STATE.md:

```markdown
# Project: {name}
**Status:** ❓ Unknown (imported)
**Created:** Pre-existing
**Updated:** {today}

## Resume Prompt
> Imported project. Update this section with current context.

## Phases
- [ ] Define phases for this project

## Open Questions
- [ ] What's the current status?
- [ ] What's the next action?
```

---

## Phase 5: Ready

**Goal:** Confirm success, show quick-start commands.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎉 YOU'RE ALL SET!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project State is ready to use.

CREATED
───────
✓ state/ACTIVE.md
✓ state/REGISTRY.md  
✓ state/config.json
✓ {n} project STATE.md files (if imported)

QUICK COMMANDS
──────────────
"project status"       → See all active projects
"new project <name>"   → Start tracking a project
"save state"           → Checkpoint current work
"resume <project>"     → Load context for a project
"close project"        → Archive when done

HOW IT WORKS DAY-TO-DAY
───────────────────────
1. I read ACTIVE.md at session start (automatic)
2. When you mention a project, I load its STATE.md
3. I save progress at checkpoints and when you ask
4. Next session, context is instantly restored

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TIP: Try "project status" now to see your projects!

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Workspace Type Presets

### Clawdbot Preset

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
  }
}
```

### Simple Preset

```json
{
  "workspaceType": "simple",
  "stateDir": "./.project-state",
  "projectsDir": "./.project-state/projects",
  "stateLocation": "separate",
  "stateFileName": "STATE.md",
  "activeFile": "./.project-state/ACTIVE.md",
  "registryFile": "./.project-state/REGISTRY.md",
  "autoSave": {
    "onCheckpoint": true,
    "onPause": true,
    "onManual": true,
    "onDecision": true
  }
}
```

### Monorepo Preset

```json
{
  "workspaceType": "monorepo",
  "stateDir": "./state",
  "projectsDir": "./packages",
  "stateLocation": "separate",
  "stateFileName": "STATE.md",
  "activeFile": "./state/ACTIVE.md",
  "registryFile": "./state/REGISTRY.md",
  "autoSave": {
    "onCheckpoint": true,
    "onPause": false,
    "onManual": true,
    "onDecision": false
  }
}
```

---

## Implementation Notes

### Path Safety

When resolving paths from config:
1. Always resolve relative to workspace root
2. Reject paths that traverse above workspace (`../../../etc/passwd`)
3. Use `path.resolve()` + verify starts with workspace path
4. Don't follow symlinks outside workspace

### Scan Safety

During workspace discovery:
1. Only read directory names, not file contents
2. Limit depth (max 2-3 levels)
3. Skip hidden directories except known ones (`.git`)
4. Skip `node_modules`, `venv`, `.cache`, etc.
5. Timeout if scan takes too long

### Minimal Permissions

The skill only needs:
- Read: workspace directory structure (names only, not contents)
- Write: state folder, STATE.md files in projects
- No network access needed
- No secrets/credentials accessed

### Security Checklist for Onboarding

- [x] Only scan directory names + file extensions, not contents
- [x] Skip files matching sensitive patterns (`.env*`, `*.key`, `secrets/`)
- [x] Show user what will be scanned BEFORE scanning
- [x] Provide opt-out for subdirectories
- [x] Don't store paths to sensitive files
- [x] Time-limit the scan (10 second timeout)
- [x] Max depth of 3 levels
- [x] Skip hidden directories (`.git/`, `.clawdbot/`, etc.)

---

## Updates Configuration

During onboarding, ask user about update preferences:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 UPDATE PREFERENCES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

How would you like to handle skill updates?

  [A] Check for updates on session start (notify only) ← default
  [B] Auto-update when new version available
  [C] Manual only (I'll update myself)

Your choice (Enter for default): _
```

### Update Commands

| Command | Action |
|---------|--------|
| `check skill updates` | Check GitHub for new version |
| `update project-state skill` | Pull latest from repo |
| `skill version` | Show current version |

### How Updates Work

1. **Check:** Compare local `currentVersion` in config.json with repo's SKILL.md version
2. **Notify:** If newer version exists, mention at session start
3. **Update:** Pull latest files from GitHub, preserve user's config.json

### Config Options

```json
{
  "updates": {
    "checkOnStart": true,       // Check for updates each session
    "autoUpdate": false,        // Auto-pull updates (use with caution)
    "repo": "kyle-pyth/ClawdSkills",
    "skillPath": "project-state",
    "lastCheck": "2026-02-25",
    "currentVersion": "1.0.0"
  }
}
```

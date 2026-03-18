---
description: Check and update CLAUDE.md memory based on changes to skills, commands, agents, and hooks.
---

# Update Memory

Check and update CLAUDE.md global memory file to keep it synchronized with source files from skills, commands, agents, and hooks.

## Overview

CLAUDE.md is a summary memory file containing:
- Skills directory structure (from `skills/`)
- Commands list (from `commands/`)
- Agents configuration (from `agents/`)
- Hooks definitions (from `hooks/`)

When these source files change, CLAUDE.md needs to be updated accordingly.

## Detection Logic

1. **Scan Source File Modification Times**
   - `~/.claude/skills/**/skill.md`
   - `~/.claude/commands/**/*.md`
   - `~/.claude/agents/**/*.md`
   - `~/.claude/hooks/**/*.{js,json}`

2. **Compare with CLAUDE.md Last Modified Time**
   - If any source file is newer than CLAUDE.md → Update needed
   - Record last sync timestamp (`~/.claude/.last-memory-sync`)

3. **Generate Report**
   - List all changed source files
   - Show CLAUDE.md sections needing updates

## Update Process

### 1. Scan Phase

```
Scanning Skills: X files
Scanning Commands: Y files
Scanning Agents: Z files
Scanning Hooks: W files
```

### 2. Compare Phase

```
Sections needing updates:
- [ ] Skills directory structure (3 skills changed)
- [ ] Commands list (1 command added)
- [ ] Agents configuration (no changes)
- [ ] Hooks definitions (2 hooks modified)
```

### 3. Confirm Update

Ask user whether to execute update:
```
Update CLAUDE.md? (yes/no/diff)
- yes: Execute update
- no: Cancel
- diff: Show detailed diff
```

### 4. Execute Update

- Preserve user-edited content (e.g., "User Background", "Tech Stack Preferences")
- Only update sections marked AUTO-GENERATED
- Update timestamp

## Usage

```
/update-memory          # Check and prompt for update
/update-memory --check  # Check only, don't update
/update-memory --force  # Force update without asking
/update-memory --diff   # Show diff comparison
```

## Output Examples

### Check Result

```
📋 CLAUDE.md Memory Status Check

Source file status:
✅ Skills: 24 files (last modified: ml-paper-writing)
✅ Commands: 14 files (last modified: update-readme)
✅ Agents: 7 files (no changes)
✅ Hooks: 5 files (last modified: session-summary)

Time comparison:
- CLAUDE.md last updated: 2024-01-15 10:30
- Source files last modified: 2024-01-16 14:22

⚠️ Changes detected, CLAUDE.md update recommended

Change details:
1. skills/ml-paper-writing/skill.md (modified at 14:22)
2. commands/update-readme.md (modified at 13:15)
3. hooks/session-summary.js (modified at 11:45)

Execute update? (yes/no/diff)
```

### Update Complete

```
✅ CLAUDE.md updated

Updated content:
- Skills directory: Synced 24 skills
- Commands list: Synced 14 commands
- Agents configuration: No changes
- Hooks definitions: Synced 5 hooks

Next sync timestamp updated.
```

## Integration Recommendations

- Integrate check reminder in `session-summary.js`
- Real-time detection in PostToolUse hook
- Recommend regular execution (e.g., at end of each session)

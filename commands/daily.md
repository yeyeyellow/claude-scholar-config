---
name: daily
description: Daily workflow management - creates Obsidian daily notes, manages TODO.md, coordinates with obsidian-research-log skill
args:
  - name: action
    description: Action (end/summary/todo/clear). Default: end
    required: false
    default: end
  - name: project
    description: Project name (for Obsidian integration)
    required: false
tags: [Workflow, Daily, TODO, Planning]
---

# /daily - Daily Workflow Management

Daily workflow command for creating logs, managing TODO.md, and planning tasks.

## Usage

### End Day (default)
```bash
/daily [project_name]
/daily end [project_name]
```
Summarizes today's work, updates daily log, updates TODO.md with pending issues and tomorrow's plans.

### Summary
```bash
/daily summary
```
Shows current TODO status.

### Update TODO
```bash
/daily todo "item1, item2, item3"
```
Quickly adds new TODO items.

### Clear Completed
```bash
/daily clear
```
Removes completed items from TODO.md.

---

## Workflow

### /daily [project_name] (default: end)

**When**: Ending work session

**Actions**:
1. Get current date (YYYY-MM-DD)
2. Collect today's work:
   - What was accomplished?
   - **What problems encountered?** (Ask for detailed problem info)
   - What to do tomorrow?
3. If project_name provided:
   - Create/Update `Research/{project-slug}/Daily/YYYY-MM-DD.md` in Obsidian vault
   - Append to existing note (preserve chronology, don't overwrite)
   - Add: completed tasks, encountered problems, tomorrow's plan
4. Update `TODO.md`:
   - Keep unsolved problems in "Pending Issues" with structured details
   - Add tomorrow's tasks to "Tomorrow's Plan"
   - Remove completed items
5. Show summary

**Problem Collection Template** (when user reports problems):
For each problem, ask/collect:
```
🐛 Description: [One-line summary]
📍 Location: [Which file/command/operation]
🔴 Error: [Error stack/message]
🔧 Attempted: [Tried solutions]
📝 Next Step: [Plan to fix]
```

**Output**:
```
✅ Daily Summary - 2026-03-17

📊 Completed:
   - [x] Task 1
   - [x] Task 2

🔧 Issues (unsolved):
   - Problem 1
   - Problem 2

📌 Tomorrow:
   - [ ] New task 1
   - [ ] New task 2

💾 Saved to: Research/{project-slug}/Daily/2026-03-17.md
📝 Updated: TODO.md
🔗 Skill: obsidian-research-log will route durable content to canonical locations
```

---

### /daily summary

**When**: Check current status

**Actions**:
1. Read TODO.md
2. Show pending items
3. Show today's daily log if exists

**Output**:
```
📋 TODO Status

🔧 Pending Issues:
   - Issue 1

📌 Upcoming Tasks:
   - [ ] Task 1
   - [ ] Task 2

📅 Today's Log: Research/{project}/Daily/2026-03-17.md
```

---

### /daily todo "item1, item2"

**When**: Quick add new TODO items

**Actions**:
1. Parse comma-separated items
2. Add to TODO.md "Tomorrow's Plan" section
3. Show updated TODO

**Output**:
```
✅ Added 2 TODO items:
   - [ ] item1
   - [ ] item2
```

---

### /daily clear

**When**: Clean up completed items

**Actions**:
1. Read TODO.md
2. Remove all checked items
3. Keep unchecked items
4. Save updated TODO.md

**Output**:
```
🧹 Cleared 3 completed items
```

---

## TODO.md Format

```markdown
# TODO

## 🔧 Pending Issues

### local-websearch Network Issues
- [ ] Fix SSL connection error (proxy-related)
  📍 `semantic_engine.py:80` | 🔴 `SSL: CERTIFICATE_VERIFY_FAILED`
  📝 Tried: Disable verify | 🔧 Next: Check proxy certificate

### /research-init Command
- [ ] Handle UnicodeEncodeError exception
  📍 Command output stream | 🔴 `UnicodeEncodeError: 'gbk' codec error`
  📝 Tried: Set PYTHONIOENCODING | 🔧 Next: Modify stdout encoding

---

## 📌 Tomorrow's Plan
- [ ] Task 1
- [ ] Task 2

---

## 🔗 Project
Project: [name]
Path: [path]
```

**Problem Entry Template**:
```markdown
### [Category]: [Brief Title]
- [ ] [Main task]
  📍 [File/Location] | 🔴 [Error type/message]
  📝 Tried: [Attempted fixes] | 🔧 Next: [Next action]
```

---

## Daily Log Format (Obsidian)

**Follows `obsidian-research-log` skill conventions**

```markdown
---
type: daily
date: 2026-03-17
title: "2026-03-17"
project: [project-slug]
tags: [daily]
---

# 2026-03-17

## 📋 Plan
- [ ] Task 1
- [ ] Task 2

## ✅ Done
- [x] Completed task

## 🐛 Issues

### SSL Error in local-websearch
📍 `semantic_engine.py:80` | 🔴 `SSL: CERTIFICATE_VERIFY_FAILED`
- Trigger: `/research-init "timber damage detection"`
- Tried: Disabled SSL verify, still fails
- Next: Check proxy certificate trust chain

## 🔍 Notes
[Temporary notes, raw logs, fragments]

## 📌 Tomorrow
- [ ] Next task

## 🔗 Links
- [[00-Hub.md]]
- [[01-Plan.md]]
```

---

## Integration Notes

### Obsidian Integration
- **Vault Path**: From `OBSIDIAN_VAULT_PATH` env var or `.claude/project-memory/registry.yaml`
- **Default Location**: `C:\Users\huangyaoyao\ObsidianVault\cc`
- **Project Slug**: Auto-generated from project_name (kebab-case)
- **Daily Folder**: `Research/{project-slug}/Daily/` (not root level)

### Knowledge Routing Rules (from `obsidian-research-log`)

**Daily/ is for process.** Route durable content to canonical locations:

| Content Type | Route To | When |
|-------------|----------|-----|
| Stable findings | `Knowledge/` | After interpretation, still relevant after weeks |
| Experiment results | `Experiments/` | When running structured experiments |
| Stable outcomes | `Results/` | When result is stable enough to reference later |
| Draft writing | `Writing/` | When result becomes claim-worthy |
| Meeting notes | `Daily/` first | Promote to `Knowledge/` or `01-Plan.md` if durable |

**Example routing**:
```
Daily/2026-03-17.md (raw notes)
    → "Found that SSL error is proxy-related"
    → Promote to Knowledge/Debugging-SSL-Issues.md
    → Link back from Daily/2026-03-17.md
```

### Coordination with `obsidian-research-log` Skill

This command **creates** daily notes. The `obsidian-research-log` skill:
- Automatically triggers when research work happens
- Routes temporary notes to canonical locations
- Updates `01-Plan.md` and `00-Hub.md` when project state changes

**Workflow**:
1. `/daily` creates `Daily/YYYY-MM-DD.md` with today's log
2. `obsidian-research-log` skill activates during session
3. Skill promotes durable content to `Knowledge/`, `Experiments/`, etc.
4. Skill updates `00-Hub.md` when milestones reached

---

## Examples

```bash
# End day (most common)
/daily

# End day with project
/daily deep-learning-timber-damage

# Check status
/daily summary

# Add new TODO items
/daily todo "fix SSL error, test encoding"

# Clean up completed
/daily clear
```

---

## Related Commands & Skills

**Commands**:
- `/checkpoint` - Create workflow checkpoints
- `/obsidian-init` - Initialize Obsidian project knowledge base
- `/obsidian-sync` - Force sync between repo and Obsidian
- `/plan` - Create implementation plans

**Skills** (auto-triggered during research work):
- `obsidian-research-log` - Routes daily notes to canonical Knowledge/Experiments/Results
- `obsidian-experiment-log` - Manages experiment and result notes
- `obsidian-project-memory` - Main project memory orchestrator

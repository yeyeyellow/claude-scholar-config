# Claude Scholar Configuration

## Project Overview

**Claude Scholar** - Semi-automated research assistant for academic research and software development

**Mission**: Support Claude Code, OpenCode, and Codex CLI across ideation, coding, experiments, writing, publication, plugin development, and project management.

---

## ⚠️ CRITICAL LANGUAGE STANDARD (全局语言标准)

**用户面向内容 MUST BE 中文 (User-facing content MUST be in Chinese)**:

| Type | MUST Be | Examples |
|------|---------|----------|
| 直接对话 | 中文 | 所有与用户的回复 |
| `/daily` 输出 | 中文 | 每日总结、明日计划 |
| `/research-init` 输出 | 中文 | 搜索进度、创建文件总结 |
| TODO.md | 中文 | 待办事项内容 |
| Daily 日志 | 中文 | `Research/*/Daily/*.md` |
| 会话总结 | 中文 | Session end summary |
| 进度报告 | 中文 | 任何向用户报告的进度 |

**配置文件保持英文 (Configuration files remain English)**:

| File Type | Language |
|-----------|----------|
| `SKILL.md` | English |
| `commands/*.md` | English |
| `memory/*.md` | English |
| `agents/*.md` | English |
| `hooks/*.js` (代码) | English |

**技术术语保持英文**: CNN, deep learning, DOI, arXiv, API, SSL, Git, etc.

---

---

## User Background

### Academic Background
- **Degree**: Computer Science PhD
- **Target Venues**:
  - Top conferences: NeurIPS, ICML, ICLR, KDD
  - High-impact journals: Nature, Science, Cell, PNAS
- **Focus**: Academic writing quality, logical coherence, natural expression

### Tech Stack Preferences

**Python Ecosystem**:
- **Package manager**: `uv` - modern Python package manager
- **Config management**: Hydra + OmegaConf (config composition, overrides, type safety)
- **Model training**: Transformers Trainer

**Git Standards**:
- **Commit convention**: Conventional Commits
  ```
  Type: feat, fix, docs, style, refactor, perf, test, chore
  Scope: data, model, config, trainer, utils, workflow
  ```
- **Branch strategy**: master/develop/feature/bugfix/hotfix/release
- **Merge strategy**: rebase for feature branch sync, merge --no-ff for integration

---

## Global Configuration

### Language Settings

**Response Language**:
- 用中文回复用户
- 技术术语保持英文（如 NeurIPS, RLHF, TDD, Git）
- 不翻译专有名词和名称

**File Content Standards** (Bilingual Rule):

**CRITICAL: 用户面向内容必须用中文 (User-facing content MUST be Chinese)**

| Content Type | Language | Examples |
|--------------|----------|----------|
| **Direct Dialogue** | 中文 | 所有与用户的对话、回复、总结 |
| **Command Output** | 中文 | `/daily`, `/research-init` 等命令的输出 |
| **TODO.md** | 中文 | 待办事项列表内容 |
| **Daily Logs** | 中文 | `Research/*/Daily/YYYY-MM-DD.md` 每日日志 |
| **Session Summary** | 中文 | 会话结束总结 |
| **Hook Output** | 中文 | `hooks/*.js` 的 console 输出 |
| **Progress Reports** | 中文 | 进度报告、状态更新 |

**Configuration Files (配置文件保持英文)**:

| File Type | Language | Reason |
|-----------|----------|--------|
| `SKILL.md` | English | Token efficiency, internal config |
| `commands/*.md` | English | Internal documentation |
| `memory/*.md` | English | Working configuration |
| `agents/*.md` | English | Agent configuration |

**Technical Terms**: 技术术语保持英文 (e.g., CNN, deep learning, DOI, arXiv, API, SSL)

Examples:
```markdown
# ❌ Wrong - 用户面向内容用英文
✅ Daily Summary - 2026-03-18
📊 Completed: Project initialization

# ✅ Correct - 用户面向内容用中文
✅ 每日总结 - 2026-03-18
📊 今日完成：项目初始化

# ✅ Correct - 配置文件用英文
## Workflow
1. Get Review Comments - Read or receive review comments
```

See `memory/language-standards.md` for detailed documentation.

### ⚠️ CRITICAL: Skill Usage Workflow (技能使用工作流)

**ALWAYS evaluate and use skills before executing tasks directly**

**3-Step Workflow**:

1. **先评估技能** (Evaluate First)
   - 收到任务后，先检查是否有相关技能
   - 扫描可用技能列表，寻找匹配项
   - 不要跳过这个步骤

2. **主动激活** (Activate Proactively)
   - 当有匹配技能时，立即使用 Skill 工具激活
   - 输出: "Activating: [skill-name] — [reason]"
   - 等待技能加载完成后再开始执行

3. **遵循工作流** (Follow Workflow)
   - 让技能来指导任务执行
   - 不要跳过技能定义的工作流程
   - 技能包含最佳实践和标准化步骤

**Skill Matching Examples**:

| Task | Should Use Skill | My Behavior |
|------|-----------------|-------------|
| 创建项目 | `create_project` | ❌ 直接操作 |
| 写代码 | `tdd` | ❌ 跳过测试 |
| 代码完成 | `code-review` | ❌ 跳过审查 |
| Git 操作 | `git-workflow` | ✅ 使用了 |
| 每日总结 | `daily` | ✅ 使用了 |
| 研究初始化 | `research-init` | ✅ 使用了 |

**Common Skills to Remember**:

- `code-review` - 代码完成后必须使用
- `tdd` - 写新功能时使用测试驱动开发
- `git-workflow` - Git 操作使用标准工作流
- `daily` - 每日工作总结
- `obsidian-project-memory` - 研究项目知识管理
- `architecture-design` - 新模块设计时使用

**Enforcement**:
- 系统提示 "Forced Skill Activation" 会自动扫描
- 必须响应匹配的技能，不要输出 "No skills needed"
- 技能优先于直接执行

### Research Workflow Standards

**Literature Search**:
- Use **local-websearch** by default (zero MCP quota)
- Do NOT use WebSearch MCP tool unless explicitly requested

**Search Scope**:
- **Focused**: Last 3 years, 20-50 papers
- **Broad**: Last 5 years, 50-100 papers
- **Languages**: English + Chinese (include CNKI)

**local-websearch Integration**:
```python
# Correct import pattern
import sys
sys.path.insert(0, 'src')  # Add src to path
from local_websearch import quick_search

# SearchResult is dataclass, NOT dict
doi = r.doi if hasattr(r, 'doi') else None  # ✅
# doi = r.get('doi', '')  # ❌ Wrong
```

**Obsidian Project Structure**:
```
Research/{project-slug}/
├── Papers/          # Paper notes (citekey.md)
├── Knowledge/       # Literature synthesis
│   ├── Literature-Overview.md
│   ├── Method-Families.md
│   └── Research-Gaps.md
├── Maps/           # Literature maps
├── Writing/        # Drafts
└── 00-Hub.md       # Project hub
```

**Paper Note Sections**: Claim, Research Question, Method, Evidence, Strengths, Limitation, Relevance, Relations, Knowledge Links

**Error Handling**:
- SSL errors: local-websearch has built-in retry, can ignore
- ModuleNotFoundError: Add `sys.path.insert(0, 'src')` before import
- AttributeError: Use `r.doi` not `r.get('doi')` (dataclass)

### Working Directory Standards
- Plan documents: `/plan` folder
- Temporary files: `/temp` folder
- Auto-create folders if they don't exist

### Task Execution Principles
- Discuss approach before breaking down complex tasks
- Run example tests after implementation
- Make backups, avoid breaking existing functionality
- Clean up temporary files after completion

### Work Style
- **Task management**: Use TodoWrite to track progress, plan before executing complex tasks, prefer existing skills
- **Communication**: Ask proactively when uncertain, confirm before important operations, follow hook-enforced workflows
- **Code style**: Python follows PEP 8, comments in English, identifiers in English

---

## Core Workflows

### Research Lifecycle (7 Stages)

```
Ideation → ML Development → Experiment Analysis → Paper Writing → Self-Review → Submission/Rebuttal → Post-Acceptance
```

| Stage | Core Tools | Commands |
|-------|-----------|----------|
| 1. Research Ideation | `research-ideation` skill + `literature-reviewer-obsidian` agent | `/research-init`, `/obsidian-review`, `/obsidian-notes` |
| 2. ML Project Dev | `architecture-design` skill + `code-reviewer` agent | `/plan`, `/commit`, `/tdd` |
| 3. Experiment Analysis | `results-analysis` skill + `data-analyst` agent | `/analyze-results` |
| 4. Paper Writing | `ml-paper-writing` skill + `paper-miner` agent | - |
| 5. Self-Review | `paper-self-review` skill | - |
| 6. Submission & Rebuttal | `review-response` skill + `rebuttal-writer` agent | `/rebuttal` |
| 7. Post-Acceptance | `post-acceptance` skill | `/presentation`, `/poster`, `/promote` |

### Development Lifecycle (Complete Workflow)

```
New Project: /create_project → /plan → /tdd → /code-review → /commit → /update-github
Existing Project:        /plan → /tdd → /code-review → /commit → /update-github
```

**Stage 0: Project Initialization (New Projects Only)**
| Command | Purpose | What it Does | What it NOT Does |
|---------|---------|--------------|------------------|
| `/create_project` | Initialize project | Create project structure from template, init uv, init Git (master+develop), create v0.1.0 tag, optional GitHub repo | ❌ No business code |
| Next: | → `/plan` | | |

**Stage 1: Design Planning**
| Command | Purpose | What it Does | What it NOT Does |
|---------|---------|--------------|------------------|
| `/plan` | Create implementation plan | Analyze requirements, create plan document, define scope and tasks | ❌ No code implementation |
| Next: | → `/tdd` | | |

**Stage 2: Test-Driven Development**
| Command | Purpose | What it Does |
|---------|---------|--------------|
| `/tdd` | Complete TDD cycle | RED → GREEN → REFACTOR loop (write test → see fail → write min code → refactor) |
| Next: | → `/code-review` | |

**Stage 3: Code Review**
| Command | Purpose | What it Does |
|---------|---------|--------------|
| `/code-review` | Review code quality | Check code quality, security, maintainability, coding standards |
| Next: | → `/commit` | |

**Stage 4: Commit**
| Command | Purpose | What it Does |
|---------|---------|--------------|
| `/commit` | Commit to local Git | Commit code (Conventional Commits format) |
| Next: | → `/update-github` or complete | |

**Stage 5: Push Remote (Optional)**
| Command | Purpose | What it Does |
|---------|---------|--------------|
| `/update-github` | Push to remote | Push code to GitHub remote repository |
| Next: | → Complete | |

**Rules:**
- Each command only does its job, nothing more
- After each command, recommend next step and wait for user confirmation

**Helper Commands (Use as Needed):**
| Command | When to Use |
|---------|-------------|
| `/setup-pm` | Configure package manager (uv/pnpm) |
| `/build-fix` | Fix build errors |
| `/refactor-clean` | Refactor and clean up code |
| `/checkpoint` | Create checkpoint |
| `/verify` | Verify changes |
| `/update-readme` | Update README documentation |
| `/update-memory` | Update CLAUDE.md memory |

### Supporting Workflows

- **Automation**: 5 Hooks auto-trigger at session lifecycle stages (skill evaluation, env init, work summary, security check)
- **Obsidian Knowledge Base**: Built-in filesystem-first project knowledge base for literature, plans, daily logs, experiments, results, writing, and archive management, with a compact vault structure and no MCP requirement
- **Knowledge Extraction**: `paper-miner` and `kaggle-miner` agents continuously extract knowledge from papers and competitions
- **Skill Evolution**: `skill-development` → `skill-quality-reviewer` → `skill-improver` three-step improvement loop

### Obsidian Project Knowledge Base Rule

- If the current repository contains `.claude/project-memory/registry.yaml`, automatically activate `obsidian-project-memory` and treat Obsidian as the default project knowledge base for this repo.
- If the repository is not yet bound but looks like a research project, automatically activate `obsidian-project-bootstrap` and import it into the vault.
- On every substantial project turn, update at least the daily note and the repo-local project memory file; touch `00-Hub.md` only when top-level project status really changes.
- Never require any extra Obsidian API configuration or API keys for this workflow.

---

## Skills Directory (46 skills)

### 🔬 Research & Analysis (4 skills)

- **research-ideation**: Research startup (5W1H, literature review, gap analysis, research question formulation)
- **results-analysis**: Experiment result analysis (statistical tests, visualization, ablation studies)
- **citation-verification**: Citation verification (multi-layer: format→API→info→content)
- **daily-paper-generator**: Daily paper generator for research tracking

### 📝 Paper Writing & Publication (7 skills)

- **ml-paper-writing**: ML/AI paper writing assistance
  - Top conferences: NeurIPS, ICML, ICLR, ACL, AAAI, COLM
  - Journals: Nature, Science, Cell, PNAS
- **writing-anti-ai**: Remove AI writing patterns, bilingual (Chinese/English)
- **paper-self-review**: Paper self-review (6-item quality checklist)
- **review-response**: Systematic rebuttal writing
- **post-acceptance**: Post-acceptance processing (presentation, poster, promotion)
- **doc-coauthoring**: Document co-authoring workflow
- **latex-conference-template-organizer**: LaTeX conference template organization

### 💻 Development Workflows (6 skills)

- **daily-coding**: Daily coding checklist (minimal mode, auto-triggered)
- **git-workflow**: Git workflow standards (Conventional Commits, branch management)
- **code-review-excellence**: Code review best practices
- **bug-detective**: Debugging and error investigation (Python, Bash/Zsh, JavaScript/TypeScript)
- **architecture-design**: ML project code architecture and design patterns
- **verification-loop**: Verification loops and testing

### 🔌 Plugin Development (8 skills)

- **skill-development**: Skill development guide
- **skill-improver**: Skill improvement tool
- **skill-quality-reviewer**: Skill quality review
- **command-development**: Slash command development
- **command-name**: Plugin structure guide
- **agent-identifier**: Agent development configuration
- **hook-development**: Hook development and event handling
- **mcp-integration**: MCP server integration

### 🧪 Tools & Utilities (4 skills)

- **planning-with-files**: Planning and progress tracking with Markdown files
- **uv-package-manager**: uv package manager usage
- **webapp-testing**: Local web application testing
- **kaggle-learner**: Kaggle competition learning

### 🧠 Obsidian Knowledge Base (10 skills)

- **obsidian-project-memory**: Default Obsidian project-memory orchestrator for repo-bound research work
- **obsidian-project-bootstrap**: Bootstrap or import a research repository into an Obsidian project knowledge base
- **obsidian-research-log**: Daily notes, plans, hub updates, and durable progress routing
- **obsidian-experiment-log**: Experiments, ablations, and result logging
- **obsidian-link-graph**: Legacy compatibility helper for repairing wikilinks across canonical notes
- **obsidian-synthesis-map**: Legacy compatibility helper for higher-level synthesis notes and comparison summaries
- **obsidian-project-lifecycle**: Detach, archive, purge, and note-level lifecycle operations
- **obsidian-literature-workflow**: Paper-note normalization and literature review inside the project vault
- **obsidian-markdown**: Vendored official Obsidian Flavored Markdown skill
- **obsidian-cli**: Vendored official Obsidian CLI skill
- **obsidian-bases / json-canvas / defuddle**: Vendored official optional support for `.base`, `.canvas`, and clean web-to-markdown extraction

### 🎨 Web Design (3 skills)

- **frontend-design**: Create distinctive, production-grade frontend interfaces
- **ui-ux-pro-max**: UI/UX design intelligence (50+ styles, 97 palettes, 57 font pairings, 9 stacks)
- **web-design-reviewer**: Visual website inspection for responsive, accessibility, and layout issues

---

## Commands (50+ Commands)

### Research Workflow Commands

| Command | Function |
|---------|----------|
| `/research-init` | Start research ideation workflow (5W1H analysis, literature review, gap analysis) |
| `/obsidian-review` | Generate project-linked literature synthesis from Obsidian paper notes |
| `/obsidian-notes` | Normalize paper notes and connect them to project knowledge, experiments, and results |
| `/obsidian-init` | Bootstrap or import an Obsidian project knowledge base for the current research repository |
| `/obsidian-ingest` | Ingest a new Markdown file or directory via classify -> promote / merge / stage-to-daily |
| `/obsidian-sync` | Force incremental or full repair sync between the repo, project memory, and Obsidian |
| `/obsidian-link` | Repair or strengthen project wikilinks across canonical notes |
| `/obsidian-note` | Archive, purge, or rename a single canonical note |
| `/obsidian-project` | Detach, archive, purge, or rebuild a project knowledge base |
| `/obsidian-views` | Explicitly generate optional `.base` views and extra canvases |
| `/analyze-results` | Analyze experiment results (statistical tests, visualization, ablation) |
| `/rebuttal` | Generate systematic rebuttal document |
| `/presentation` | Create conference presentation outline |
| `/poster` | Generate academic poster design |
| `/promote` | Generate promotion content (Twitter, LinkedIn, blog) |

### Development Workflow Commands

| Command | Function |
|---------|----------|
| `/plan` | Create implementation plan |
| `/commit` | Commit code (following Conventional Commits) |
| `/update-github` | Commit and push to GitHub |
| `/update-readme` | Update README documentation |
| `/code-review` | Code review |
| `/tdd` | Test-driven development workflow |
| `/build-fix` | Fix build errors |
| `/verify` | Verify changes |
| `/checkpoint` | Create checkpoint |
| `/refactor-clean` | Refactor and clean up |
| `/learn` | Extract reusable patterns from code |
| `/create_project` | Create new project |
| `/setup-pm` | Configure package manager (uv/pnpm) |
| `/update-memory` | Check and update CLAUDE.md memory |

### SuperClaude Command Suite (`/sc`)

- `/sc agent` - Agent dispatch
- `/sc analyze` - Code analysis
- `/sc brainstorm` - Interactive brainstorming
- `/sc build` - Build project
- `/sc business-panel` - Business panel
- `/sc cleanup` - Code cleanup
- `/sc design` - System design
- `/sc document` - Generate documentation
- `/sc estimate` - Effort estimation
- `/sc explain` - Code explanation
- `/sc git` - Git operations
- `/sc help` - Help info
- `/sc implement` - Feature implementation
- `/sc improve` - Code improvement
- `/sc index` - Project index
- `/sc index-repo` - Repository index
- `/sc load` - Load context
- `/sc pm` - Package manager operations
- `/sc recommend` - Recommend solutions
- `/sc reflect` - Reflection summary
- `/sc research` - Technical research
- `/sc save` - Save context
- `/sc select-tool` - Tool selection
- `/sc spawn` - Spawn subtasks
- `/sc spec-panel` - Spec panel
- `/sc task` - Task management
- `/sc test` - Test execution
- `/sc troubleshoot` - Issue troubleshooting
- `/sc workflow` - Workflow management

---

## Agents (16 Agents)

### Research Workflow Agents

- **literature-reviewer** - Literature search, classification, and trend analysis (web search, paper discovery)
- **literature-reviewer-obsidian** - Filesystem-first literature review from the Obsidian project knowledge base
- **research-knowledge-curator-obsidian** - Default curator for project plans, daily logs, literature, experiments, results, and writing in Obsidian
- **data-analyst** - Automated data analysis and visualization
- **rebuttal-writer** - Systematic rebuttal writing with tone optimization
- **paper-miner** - Extract writing knowledge from successful papers

### Development Workflow Agents

- **architect** - System architecture design
- **build-error-resolver** - Build error fixing
- **bug-analyzer** - Deep code execution flow analysis and root cause investigation
- **code-reviewer** - Code review
- **dev-planner** - Development task planning and breakdown
- **refactor-cleaner** - Code refactoring and cleanup
- **tdd-guide** - TDD workflow guidance
- **kaggle-miner** - Extract engineering practices from Kaggle solutions

### Design & Content Agents

- **ui-sketcher** - UI blueprint design and interaction specifications
- **story-generator** - User story and requirement generation

---

## Hooks (5 Hooks)

Cross-platform Node.js hooks for automated workflow execution:

| Hook | Trigger | Function |
|------|---------|----------|
| `session-start.js` | Session start | Show Git status, todos, commands, and bound Obsidian project-memory status |
| `skill-forced-eval.js` | Every user input | Force evaluate all available skills and hint bound-repo Obsidian curator flow on research turns |
| `session-summary.js` | Session end | Generate work log, detect CLAUDE.md updates, and remind minimum Obsidian write-back for bound repos |
| `stop-summary.js` | Session stop | Quick status check, temp file detection, and bound-repo Obsidian maintenance reminder |
| `security-guard.js` | File operations | Security validation (key detection, dangerous command interception) |

---

## Rules (4 Rules)

Global constraints, always active:

| Rule File | Purpose |
|-----------|---------|
| `coding-style.md` | ML project code standards: 200-400 line files, immutable config, type hints, Factory & Registry patterns |
| `agents.md` | Agent orchestration: auto-invocation timing, parallel execution, multi-perspective analysis |
| `security.md` | Security standards: key management, sensitive file protection, pre-commit security checks |
| `experiment-reproducibility.md` | Experiment reproducibility: random seeds, config recording, environment recording, checkpoint management |

---

## Naming Conventions

### Skill Naming
- Format: kebab-case (lowercase + hyphens)
- Form: prefer gerund form (verb+ing)
- Example: `scientific-writing`, `git-workflow`, `bug-detective`

### Tags Naming
- Format: Title Case
- Abbreviations all caps: TDD, RLHF, NeurIPS, ICLR
- Example: `[Writing, Research, Academic]`

### Description Standards
- Person: third person
- Content: include purpose and use cases
- Example: "Provides guidance for academic paper writing, covering top-venue submission requirements"

---

## Task Completion Summary

After each task, proactively provide a brief summary in **Chinese**:

```
📋 操作总结
1. [主要操作]
2. [修改的文件]

📊 当前状态
• [Git/文件系统/运行状态]

💡 下一步
1. [建议的后续操作]
```

**CRITICAL**: Always use Chinese for task summaries and user-facing communications.

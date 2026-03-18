# Claude Scholar Memory

## Session History

### 2026-03-18: GitHub Repository Created & Skill Usage Workflow Improvement

**Context**: User created GitHub repository for Claude Scholar configuration and identified critical issue with skill usage.

**Completed Work**:

1. **GitHub Repository Creation**:
   - Created repo: https://github.com/yeyeyellow/claude-scholar-config
   - Initialized git in `C:/Users/huangyaoyao/.claude`
   - Created `.gitignore` (excludes sensitive files: settings.json, projects/, logs/, etc.)
   - Created `README.md` (project overview in Chinese)
   - Committed 659 files (CLAUDE.md, agents, commands, skills, hooks, rules)
   - Used `git-workflow` skill for push operation ✅

2. **Critical Issue Identified - Skill Not Being Used**:
   - **Problem**: Claude has many specialized skills but wasn't using them
   - **Examples of missed opportunities**:
     - Created project structure manually instead of using `create_project`
     - Wrote code directly instead of using `tdd`
     - Finished features without `code-review`
   - **User feedback**: "明明你有很多的技能可以用，但是每次你都不用"

3. **Skill Usage Workflow Added to Global Memory**:
   - Added to `CLAUDE.md` (全局配置)
   - Added to `memory/MEMORY.md` (项目记忆)
   - Defined 3-step mandatory workflow:
     1. 先评估技能 (Evaluate First)
     2. 主动激活 (Activate Proactively)
     3. 遵循工作流 (Follow Workflow)
   - Created skill matching examples table
   - Listed common skills to remember

**Key Changes Made**:
- GitHub repository now hosts all configuration files
- `.gitignore` properly excludes sensitive data (settings.json, projects/, sessions/, etc.)
- Skill usage workflow is now permanently recorded in global memory
- Future sessions will follow skill-first approach

**Root Cause Analysis**:
- Error 1: Not using skills → Fixed by adding workflow to global memory
- Error 2: Direct execution instead of skill-based → Fixed by defining mandatory 3-step process
- Error 3: "No skills needed" default response → Fixed by creating skill matching examples

**Next Steps**:
- Always evaluate skills before executing tasks
- Use Skill tool proactively when match found
- Follow skill-defined workflows instead of skipping them

---

### 2026-03-18: Configuration Files English Conversion

**Context**: User requested conversion of all working configuration files from Chinese to English for token efficiency.

**Completed Work**:

1. **Memory Files (3 files)**:
   - `memory/research-config.md` - Converted to English
   - `memory/research-workflow-errors.md` - Converted to English
   - `memory/research-init-postmortem.md` - Converted to English

2. **Skill Files (3 files)**:
   - `skills/research-ideation/SKILL.md` - Updated to use local-websearch instead of WebSearch
   - `skills/research-ideation/references/literature-search-strategies.md` - Updated to local-websearch workflow
   - `skills/research-ideation/references/obsidian-integration-guide.md` - NEW FILE (replaces zotero-integration-guide.md)
   - `skills/research-ideation/references/zotero-integration-guide.md` - DELETED

3. **Command Files (9 files)** - All converted to English:
   - `commands/research-init.md` (395 lines)
   - `commands/daily.md` (322 lines)
   - `commands/create_project.md` (240 lines)
   - `commands/update-github.md` (103 lines)
   - `commands/rebuttal.md` (156 lines)
   - `commands/sc/pm.md` (593 lines) - Major rewrite, converted Japanese/Chinese to English
   - `commands/update-readme.md` (135 lines)
   - `commands/update-memory.md` (93 lines)
   - `commands/analyze-results.md` (134 lines)

4. **Agent Files (1 file)**:
   - `agents/literature-reviewer-obsidian.md` - Removed WebSearch and WebFetch tools

**Key Changes Made**:
- Replaced WebSearch (MCP quota consuming) with local-websearch (zero MCP quota) as default search method
- Removed all Zotero integration references, replaced with Obsidian-native workflow
- Converted all Chinese/Japanese user-facing text to English while preserving technical accuracy
- Exception: Files with "zh" or "cn" in filename remain Chinese (intentionally user-facing)

**Root Cause Analysis**:
- Error 1: Used WebSearch instead of local-websearch → Fixed by updating skill documentation
- Error 2: Chinese content in working configs → Fixed by systematic file conversion
- Error 3: Zotero integration in workflow → Fixed by removal and replacement with Obsidian

**Next Steps** (if needed):
- Convert remaining skill files (82 files identified with Chinese content)
- Focus on reference/ directories in actively-used skills
- Many example files may intentionally stay Chinese (e.g., writing-anti-ai/examples/chinese.md)

---

## Research Configuration

### Obsidian Integration
- **Vault Path**: `C:\Users\huangyaoyao\ObsidianVault\cc`
- **Research Directory**: `Research/`
- **Project Structure**: `Research/{project-slug}/` with Papers/, Knowledge/, Maps/, Writing/

### local-websearch Configuration
- **Path**: `C:\Users\huangyaoyao\Code\local-websearch`
- **Venv**: `.venv/Scripts/python.exe`
- **Default Scope**: `focused` (3 years, 15 results per query)
- **Default Languages**: `["en", "zh"]`

### Key Principles
1. **Zero MCP quota**: Use local-websearch by default, not WebSearch
2. **Obsidian-native**: Filesystem-first literature management, no Zotero
3. **Language Standards**: Config files in English, user interface in Chinese (see `language-standards.md`)
4. **SearchResult handling**: Use attribute access (`r.doi`), not dict methods (`r.get('doi')`)
5. **Skill-First Workflow**: ALWAYS evaluate and use skills before executing tasks directly

### Skill Usage Workflow (CRITICAL - 必须遵循的技能使用流程)

**Problem Identified (2026-03-18)**: Claude was not using available skills, executing tasks directly instead of using specialized workflows.

**3-Step Mandatory Workflow**:

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

| Task | Should Use Skill | Previous Behavior | Correct Behavior |
|------|-----------------|-------------------|------------------|
| 创建项目 | `create_project` | ❌ 直接操作 | ✅ 使用 skill |
| 写代码 | `tdd` | ❌ 跳过测试 | ✅ TDD 流程 |
| 代码完成 | `code-review` | ❌ 跳过审查 | ✅ 自动审查 |
| Git 操作 | `git-workflow` | ✅ 使用了 | ✅ 继续使用 |
| 每日总结 | `daily` | ✅ 使用了 | ✅ 继续使用 |
| 研究初始化 | `research-init` | ✅ 使用了 | ✅ 继续使用 |

**Common Skills to Remember**:

| Category | Skills | Use When |
|----------|--------|----------|
| Development | `tdd`, `code-review`, `git-workflow` | Writing code, finishing features |
| Research | `research-init`, `obsidian-research-log` | Starting research, daily notes |
| Planning | `plan`, `architecture-design` | Complex tasks, new modules |
| Quality | `verification-loop`, `bug-detective` | Testing, debugging |

**Enforcement**: System hook "Forced Skill Activation" will automatically scan and prompt for matching skills.

### Language Standards (CRITICAL - User-Facing Content)

**MUST BE CHINESE** (用户面向的内容必须是中文):
- Direct dialogue output with user (所有直接与用户对话的输出)
- Daily summaries (每日总结)
- TODO.md content (TODO.md 文件内容)
- Daily log files (每日日志文件)
- All user-facing text (所有用户面向的文本)

**REMAIN ENGLISH** (配置/工作文件保持英文):
- Skill config files (技能配置文件 `SKILL.md`)
- Command config files (命令配置文件)
- Memory files (记忆文件 `memory/`)
- Agent config files (Agent 配置)
- Hook config files (Hook 配置)
- Technical documentation (技术文档)

**Technical Terms**: Keep technical terms in English (e.g., CNN, deep learning, DOI, arXiv)

**Examples**:
```
✅ Correct (Chinese):
- 📊 今日完成
- 📌 明日计划
- [ ] 审阅高相关性论文

❌ Wrong (English):
- 📊 Completed
- 📌 Tomorrow's Plan
- [ ] Review high-relevance papers
```

### Common Workflows

#### Research Init
```bash
/research-init "research topic" [focused|broad] [review|proposal|both]
```

#### Daily Workflow
```bash
/daily [project_name]     # End day summary
/daily summary            # Check status
/daily todo "item1, item2" # Add TODO items
/daily clear              # Clean up completed items
```

---

## Technical Notes

### SearchResult Dataclass Pattern
```python
# Correct import method
import sys
sys.path.insert(0, 'src')  # Add src to path
from local_websearch import quick_search

# Correct way to access dataclass
doi = r.doi if hasattr(r, 'doi') else None

# Convert to dict for JSON serialization
paper_dict = {
    'title': r.title if hasattr(r, 'title') else '',
    'authors': r.authors if hasattr(r, 'authors') else [],
    'doi': r.doi if hasattr(r, 'doi') else None,
}
```

### Obsidian Note Templates

#### Paper Note
```markdown
---
type: paper
title: "Paper Title"
project: {project-slug}
authors: [...]
year: YYYY
venue: "Venue"
doi: "DOI"
citekey: {author}{year}{keyword}
status: to-read
---
```

#### Project Hub
```markdown
---
type: project-hub
title: "Project Title"
project: {project-slug}
created: YYYY-MM-DD
status: active
---
```

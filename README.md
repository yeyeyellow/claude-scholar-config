# Claude Scholar Configuration

Claude Scholar - 半自动化研究和软件开发助手配置

## 项目概述

这是为 Claude Code 配置的个性化研究助手，支持：

- **研究工作流**: 文献调研、论文写作、同行评议回应
- **开发工作流**: TDD、代码审查、Git 管理
- **Obsidian 集成**: 基于文件系统的知识库管理
- **自动化**: 5 个 Hooks 自动触发工作流程

## 目录结构

```
.claude/
├── CLAUDE.md              # 全局配置文件
├── agents/                # Agent 配置
├── commands/              # 斜杠命令
├── skills/                # 技能配置
├── hooks/                 # 自动化 Hooks
├── rules/                 # 全局规则
├── projects/              # 项目记忆
└── plugins/               # 插件配置
```

## 核心功能

### 研究生命周期

```
Ideation → ML Development → Experiment Analysis → Paper Writing →
Self-Review → Submission/Rebuttal → Post-Acceptance
```

### 开发生命周期

```
New Project: /create_project → /plan → /tdd → /code-review → /commit → /update-github
Existing Project: /plan → /tdd → /code-review → /commit → /update-github
```

## 主要命令

### 研究工作流

| 命令 | 功能 |
|------|------|
| `/research-init` | 启动研究调研工作流 |
| `/obsidian-review` | 生成文献综述 |
| `/obsidian-notes` | 标准化论文笔记 |
| `/analyze-results` | 分析实验结果 |
| `/rebuttal` | 生成系统性反驳文档 |

### 开发工作流

| 命令 | 功能 |
|------|------|
| `/plan` | 创建实现计划 |
| `/tdd` | 测试驱动开发 |
| `/code-review` | 代码审查 |
| `/commit` | 提交代码 |
| `/update-github` | 推送到 GitHub |

### 日常工作流

| 命令 | 功能 |
|------|------|
| `/daily` | 每日总结 |
| `/daily summary` | 查看状态 |
| `/daily todo "item"` | 添加待办 |
| `/daily clear` | 清理已完成 |

## 语言标准

**用户面向内容必须用中文**:
- 直接对话输出
- `/daily` 输出
- TODO.md 内容
- Daily 日志文件

**配置文件保持英文**:
- `SKILL.md`
- `commands/*.md`
- `memory/*.md`

**技术术语保持英文**: CNN, deep learning, DOI, arXiv, etc.

## Obsidian 集成

- **Vault Path**: `C:\Users\huangyaoyao\ObsidianVault\cc`
- **Research Directory**: `Research/`
- **项目结构**: `Research/{project-slug}/` with Papers/, Knowledge/, Maps/, Writing/

## 技术栈

- **包管理器**: `uv`
- **配置管理**: Hydra + OmegaConf
- **文献搜索**: local-websearch (零 MCP 配额)
- **Git 规范**: Conventional Commits

## 许可证

MIT License

---

**作者**: Claude Scholar User
**灵感来源**: [Galaxy-Dawn/claude-scholar](https://github.com/Galaxy-Dawn/claude-scholar)

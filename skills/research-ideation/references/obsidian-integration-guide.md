# Obsidian Native Literature Management Guide

Filesystem-first literature management and knowledge organization through Obsidian vault.

## 1. Project Directory Structure

### 1.1 Standard Structure

```
Research/{project-slug}/
├── 00-Hub.md                      # Project hub
├── Papers/                        # Paper notes
│   ├── author2026keyword1.md
│   └── ...
├── Knowledge/                     # Knowledge synthesis
│   ├── Literature-Overview.md
│   ├── Method-Families.md
│   └── Research-Gaps.md
├── Maps/                          # Optional: literature maps
│   └── literature.canvas
└── Writing/                       # Output files
    ├── literature-review.md
    └── research-proposal.md
```

## 2. Paper Note Template

### 2.1 Frontmatter Metadata

Each paper note contains:

```yaml
---
type: paper
title: "Paper Title"
project: {project-slug}
authors:
  - First Author
  - Second Author
year: 2026
venue: "NeurIPS"
doi: "10.xxxx/xxxxx"
url: "https://arxiv.org/abs/xxxx.xxxxx"
citekey: author2026keyword
status: to-read            # to-read, reading, done
keywords:
  - deep learning
  - computer vision
concepts: []
methods: []
related_papers: []
linked_knowledge: []
---
```

### 2.2 Citekey Generation Rule

Format: `{first_author}{year}{first_keyword}`

Examples:
- `zhang2026monocular` - Zhang 2026 paper about monocular vision
- `li2025crack` - Li 2025 paper about crack detection

### 2.3 Note Content Structure

```markdown
## {Paper Title}

**Authors**: {Author list}
**Year**: {Year}
**Venue**: {Venue}

## Abstract
{Abstract content}

## Claim
{Core claim of the paper}

## Research Question
{Research question}

## Method
{Method overview}

## Evidence
{Key evidence}

## Strengths
{Strengths}

## Limitation
{Limitations}

## Direct Relevance to Repo
{Direct relevance to current project}

## Relation to Other Papers
{Relationship to other papers}

## Knowledge Links
{Knowledge links}
```

## 3. Literature Search and Note Creation

### 3.1 Search with local-websearch

```python
import sys
sys.path.insert(0, 'src')
from local_websearch import quick_search

results = quick_search(
    query_string="monocular depth estimation",
    max_results=15,
    year_from=2023,
    sources=['arxiv', 'semantic', 'openalex', 'crossref']
)
```

### 3.2 Batch Create Notes

Auto-generate paper notes from search results:

```python
def create_paper_notes(results, project_slug):
    """Batch create paper notes"""
    for paper in results:
        citekey = generate_citekey(paper['authors'], paper['year'], paper['title'])
        content = generate_note_template(paper, citekey, project_slug)
        write_note(f"Papers/{citekey}.md", content)
```

## 4. Wikilink Knowledge Network

### 4.1 Paper-to-Paper Links

```markdown
## Relation to Other Papers
- [[zhang2026monocular]] - Similar monocular vision approach
- [[li2025crack]] - Focuses on different damage types
```

### 4.2 Method and Concept Links

```markdown
## Method
Uses [[knowledge/Method-Families#Transformer|Transformer]] architecture...
```

### 4.3 Knowledge Synthesis Links

```markdown
## Knowledge Links
- [[Knowledge/Literature-Overview]] - Literature overview
- [[Knowledge/Method-Families]] - Method classification
```

## 5. Project Hub Management

### 5.1 Hub Content Structure

```markdown
---
type: project-hub
title: "Research Project Title"
project: {project-slug}
created: 2026-03-18
status: active
---

# Research Project Title

## Research Overview
{Project overview}

## Progress
- [x] Project initialization
- [x] Literature search (N papers)
- [ ] Paper reading and analysis
- [ ] Method implementation
- [ ] Experimental validation

## Key Research Questions
1. {Research question 1}
2. {Research question 2}

## Related Papers
{Related paper list}

## Changelog
| Date | Content |
|------|--------|
| 2026-03-18 | Project initialization |
```

## 6. Search and Query

### 6.1 Obsidian Search Syntax

- **By type**: `type:paper` searches all paper notes
- **By project**: `project:jiarong-timber` searches specific project
- **By status**: `status:to-read` searches unread papers
- **By keyword**: `monocular vision` keyword search

### 6.2 Dataview Query Examples

```dataview
TABLE without ID
  link(file.link, title) AS "Paper",
  authors[0] AS "First Author",
  year AS "Year",
  status AS "Status"
FROM "Research/jiarong-timber-damage-quantification/Papers"
WHERE type = "paper"
SORT year DESC
```

## 7. Workflow Integration

### 7.1 Complete Workflow

```
/research-init (Project initialization)
    ↓
Create Obsidian project structure
    ↓
local-websearch literature search
    ↓
Batch create paper notes
    ↓
Read and annotate notes
    ↓
Generate knowledge synthesis notes
    ↓
/obsidian-review (Literature review)
    ↓
Paper writing
```

### 7.2 Integration with Other Tools

| Tool | Purpose |
|------|---------|
| local-websearch | Literature search (zero MCP quota) |
| Obsidian CLI | Note creation, search, management |
| Obsidian Canvas | Literature relationship visualization |
| Dataview | Structured queries |

## 8. Common Issues

### 8.1 Note File Management

- **Deduplication**: Identify duplicate notes via citekey
- **Archive**: Use `status:archived` for irrelevant papers
- **Delete**: Check wikilink references before deletion

### 8.2 Managing Large Paper Sets

- Group by subtopic using folders
- Use tags for multi-dimensional labeling
- Regular cleanup and archival

### 8.3 Cross-Project References

- Use absolute paths or vault root references
- Or place shared papers in `Papers/Shared/` directory

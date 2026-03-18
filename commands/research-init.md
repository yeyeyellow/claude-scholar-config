---
name: research-init
description: Initialize a research project with Obsidian-native literature review. Uses local-websearch (zero MCP quota) with pre-configured environment. Reads config from memory/research-config.md.
args:
  - name: topic
    description: Research topic or keywords
    required: true
  - name: scope
    description: Review scope (focused/broad)
    required: false
    default: focused
  - name: output_type
    description: Output type (review/proposal/both)
    required: false
    default: both
  - name: use_mcp_search
    description: Use WebSearch with MCP quota (consumes quota). Default: false (use local-websearch)
    required: false
    default: false
tags: [Research, Literature Review, Obsidian, Paper Search]
---

# /research-init - Obsidian-Native Research Startup Workflow

Launch a complete literature survey workflow for the research topic "$topic".

**Config Priority**: memory/research-config.md → Command args → Defaults

## Quick Start

```bash
# Use default config (read from research-config.md)
/research-init "timber building damage detection"

# Specify scope
/research-init "transformer interpretability" broad

# Generate only literature review
/research-init "graph neural networks" focused review
```

## Workflow (Execute in Order)

### Step 0: Read Configuration

1. **Read from `memory/research-config.md` first**:
   - `obsidian_vault`: Obsidian vault path
   - `research_dir`: Research directory name
   - `local_websearch_path`: local-websearch project path
   - `local_websearch_venv`: Python venv path
   - `default_scope`: Default search scope
   - `default_languages`: Default search languages
   - `default_output`: Default output type

2. **Command args override config file**

### Step 1: Environment Check (Skip Known Errors)

```bash
# Check Obsidian vault
if [ ! -d "$OBSIDIAN_VAULT" ]; then
    echo "Error: Obsidian vault not found: $OBSIDIAN_VAULT"
    echo "Please configure memory/research-config.md"
    exit 1
fi

# Check local-websearch (only when using local search)
if [ "$use_mcp_search" = "false" ]; then
    if [ ! -d "$LOCAL_WEBSEARCH_PATH" ]; then
        echo "Error: local-websearch path not found: $LOCAL_WEBSEARCH_PATH"
        exit 1
    fi
    if [ ! -f "$LOCAL_WEBSEARCH_PATH/.venv/Scripts/python.exe" ]; then
        echo "Error: local-websearch venv not found"
        exit 1
    fi
fi
```

### Step 2: Create Project Structure

```bash
# Generate project slug (kebab-case)
PROJECT_SLUG=$(echo "$topic" | sed 's/ /-/g' | tr '[:upper:]' '[:lower:]')

# Create directories
mkdir -p "$OBSIDIAN_VAULT/$RESEARCH_DIR/${PROJECT_SLUG}/"{Papers,Knowledge,Maps,Writing}

# Create project Hub
cat > "$OBSIDIAN_VAULT/$RESEARCH_DIR/${PROJECT_SLUG}/00-Hub.md" << EOF
---
type: project-hub
title: "${topic} Research Project"
project: ${PROJECT_SLUG}
created: $(date +%Y-%m-%d)
status: active
---

# ${topic} Research Project

[Project initializing...]
EOF
```

### Step 3: Literature Search

**Key**: Use correct local-websearch invocation

```bash
# Use project venv Python, correctly import module
cd "$LOCAL_WEBSEARCH_PATH"
"$LOCAL_WEBSEARCH_PATH/.venv/Scripts/python.exe" -c "
import sys
sys.path.insert(0, 'src')  # Key: add src to path
from local_websearch import quick_search
import json

# Search parameters
queries = [
    '${topic}',  # Main keyword
    '${topic} detection',
    '${topic} monitoring'
]

all_results = []
for query in queries:
    results = quick_search(
        query_string=query,
        max_results=${MAX_RESULTS:-15},  # focused: 15, broad: 25
        year_from=${YEAR_FROM:-2023},     # focused: current-3, broad: current-5
        sources=['arxiv', 'semantic', 'openalex', 'crossref']
    )
    all_results.extend(results)

# Deduplicate and convert to dict (Key: SearchResult is dataclass, not dict)
seen_dois = set()
unique_results = []
for r in all_results:
    doi = r.doi if hasattr(r, 'doi') else None
    if doi and doi not in seen_dois:
        seen_dois.add(doi)
        unique_results.append({
            'title': r.title if hasattr(r, 'title') else '',
            'authors': r.authors if hasattr(r, 'authors') else [],
            'year': r.year if hasattr(r, 'year') else None,
            'doi': doi,
            'abstract': r.abstract if hasattr(r, 'abstract') else '',
            'url': r.url if hasattr(r, 'url') else '',
            'venue': r.venue if hasattr(r, 'venue') else ''
        })

# Save results
with open('${OUTPUT_PATH}/search_results.json', 'w', encoding='utf-8') as f:
    json.dump(unique_results, f, ensure_ascii=False, indent=2)

print(f'Found {len(unique_results)} papers')
"
```

**SearchResult Object Handling**:
```python
# ❌ Wrong
doi = r.get('doi', '')  # SearchResult has no get method

# ✅ Correct
doi = r.doi if hasattr(r, 'doi') else None

# ✅ Convert to dict first
paper_dict = {
    'title': r.title if hasattr(r, 'title') else '',
    'authors': r.authors if hasattr(r, 'authors') else [],
    'doi': r.doi if hasattr(r, 'doi') else None,
}
```

### Step 4: Create Paper Notes

```python
import json
import re
from pathlib import Path

def generate_citekey(authors, year, title):
    '''Generate citekey: {first_author}{year}{first_keyword}'''
    if authors and len(authors) > 0:
        first_author = authors[0].split()[-1].lower()
        first_author = re.sub(r'[^a-z]', '', first_author)
    else:
        first_author = 'anonymous'

    words = title.lower().split()
    keywords = [w for w in words if len(w) > 3 and w not in ['the', 'this', 'that', 'with', 'from']]
    keyword = keywords[0] if keywords else 'paper'

    return f'{first_author}{year}{keyword}'

# Read search results
with open('search_results.json', 'r', encoding='utf-8') as f:
    papers = json.load(f)

papers_dir = Path('Papers')
created = 0
skipped = 0

for paper in papers:
    citekey = generate_citekey(paper['authors'], paper['year'], paper['title'])

    # Check duplicate
    if (papers_dir / f'{citekey}.md').exists():
        skipped += 1
        continue

    # Create note
    content = f'''---
type: paper
title: "{paper['title']}"
project: {PROJECT_SLUG}
authors: {"\\n  - ".join(paper['authors'][:5])}
year: {paper['year']}
venue: "{paper.get('venue', 'Unknown')}"
doi: "{paper.get('doi', '')}"
url: "{paper.get('url', '')}"
citekey: {citekey}
status: to-read
keywords:
  - {PROJECT_SLUG}
concepts: []
methods: []
related_papers: []
linked_knowledge: []
---

## {paper['title']}

**Authors**: {", ".join(paper['authors'][:3])}{" et al." if len(paper['authors']) > 3 else ""}
**Year**: {paper['year']}
**Venue**: {paper.get('venue', 'Unknown')}

## Abstract
{paper.get('abstract', 'No abstract available.')}

## Claim

## Research Question

## Method

## Evidence

## Strengths

## Limitation

## Direct Relevance to Repo

## Relation to Other Papers

## Knowledge Links
'''

    (papers_dir / f'{citekey}.md').write_text(content, encoding='utf-8')
    created += 1

print(f'Created {created} notes, skipped {skipped} existing')
```

### Step 5: Create Knowledge Synthesis Notes

Auto-generate based on search results:
- `Knowledge/Literature-Overview.md` - Literature overview
- `Knowledge/Method-Families.md` - Method classification
- `Knowledge/Research-Gaps.md` - Research gaps

### Step 6: Generate Output Files

Based on `output_type` generate:
- `Writing/literature-review.md` - Literature review
- `Writing/research-proposal.md` - Research proposal

### Step 7: Update Project Hub

```markdown
## Research Progress
- [x] Project initialization
- [x] Literature search (N papers)
- [x] Paper notes created (N notes)
- [x] Literature synthesis
- [x] Output files generated
```

## Config File Example

`memory/research-config.md`:

```yaml
# Obsidian config
obsidian_vault: "C:\Users\huangyaoyao\ObsidianVault\cc"
research_dir: "Research/"

# local-websearch config
local_websearch_path: "C:\Users\huangyaoyao\Code\local-websearch"
local_websearch_venv: ".venv/Scripts/python.exe"

# Default search parameters
default_scope: "focused"  # focused | broad
default_languages: ["en", "zh"]
default_output: "both"    # review | proposal | both

# Search scope config
focused:
  years: 3
  max_results_per_query: 15
broad:
  years: 5
  max_results_per_query: 25
```

## Common Errors and Solutions

### Error 1: ModuleNotFoundError

```
ModuleNotFoundError: No module named 'local_websearch'
```

**Cause**: local-websearch in `src/` subdirectory
**Solution**:
```python
import sys
sys.path.insert(0, 'src')  # Add src to path
from local_websearch import quick_search
```

### Error 2: AttributeError

```
AttributeError: 'SearchResult' object has no attribute 'get'
```

**Cause**: SearchResult is dataclass, not dict
**Solution**:
```python
# Use attribute access
doi = r.doi if hasattr(r, 'doi') else None

# Or convert to dict
paper_dict = {'title': r.title, 'authors': r.authors, ...}
```

### Error 3: SSL Error

```
[SSL: UNEXPECTED_EOF_WHILE_READING]
```

**Cause**: Network connection issue
**Handling**: local-websearch has built-in retry, can ignore or lower log level

## Completion Checklist

- [ ] Obsidian project structure created
- [ ] Literature search completed (target: focused 20-50, broad 50-100 papers)
- [ ] Paper notes created (deduplicated)
- [ ] Knowledge synthesis notes created
- [ ] Output files generated (based on output_type)
- [ ] Project hub updated
- [ ] Errors logged to memory (if new errors)

## Output Structure

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
├── Maps/                          # Optional: literature map
│   └── literature.canvas
├── Writing/                       # Output files
│   ├── literature-review.md
│   └── research-proposal.md
└── search_results.json            # Raw search results
```

## Related Resources

- **Config file**: `memory/research-config.md`
- **Error log**: `memory/research-workflow-errors.md`
- **Skill**: `research-ideation`
- **Commands**: `/obsidian-init`, `/obsidian-review`

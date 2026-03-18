---
name: analyze-results
description: Analyze experimental results and generate statistical analysis for paper writing. Triggers data-analyst agent to perform comprehensive analysis following academic standards.
args:
  - name: data_path
    description: Path to experimental results (CSV, JSON, or directory)
    required: false
  - name: analysis_type
    description: Type of analysis (full, comparison, ablation, visualization)
    required: false
    default: full
tags: [Research, Analysis, Statistics, Paper Writing]
---

# Analyze Results Command

Quickly launch experiment result analysis workflow and generate publication-quality statistical analysis and visualization.

## Usage

### Basic Usage

```bash
/analyze-results
```

Interactive mode - will prompt for data location and analysis type.

### Specify Data Path

```bash
/analyze-results path/to/results.csv
```

Analyze specified experiment results file.

### Specify Analysis Type

```bash
/analyze-results path/to/results/ comparison
```

Perform model comparison analysis on results in specified directory.

## Analysis Types

| Type | Description | Output |
|------|-------------|--------|
| `full` | Complete analysis (default) | Statistical analysis + visualization + Results draft |
| `comparison` | Model comparison | Performance comparison + significance testing |
| `ablation` | Ablation study | Component contribution analysis |
| `visualization` | Visualization generation | Publication-quality chart specifications |

## Workflow

1. **Data Location** - Find experiment result files
2. **Data Validation** - Check data completeness and format
3. **Statistical Analysis** - Execute pre-tests and hypothesis tests
4. **Report Generation** - Create analysis report and Results draft
5. **Visualization Specs** - Provide chart generation specifications

## Output Files

After command execution, the following files are generated:

```
analysis-output/
├── analysis-report.md          # Analysis report (statistical summary, key findings)
├── results-draft.md            # Results section draft (ready for paper)
└── visualization-specs.md      # Visualization specs (detailed chart descriptions)
```

## Examples

### Example 1: Analyze Single Experiment Result

```bash
/analyze-results experiments/model_performance.csv
```

**Output**:
- Basic statistics (mean, std, confidence intervals)
- Comparison with baseline
- Statistical significance test results
- Paper Results section draft

### Example 2: Compare Multiple Models

```bash
/analyze-results experiments/comparison/ comparison
```

**Output**:
- Multi-model performance comparison table
- Statistical significance tests (t-test, ANOVA)
- Effect size analysis (Cohen's d, η²)
- Comparison visualization specifications

### Example 3: Ablation Study Analysis

```bash
/analyze-results experiments/ablation/ ablation
```

**Output**:
- Component contribution analysis
- Performance degradation quantification
- Ablation study table
- Component importance ranking

## Integration Notes

This command triggers the **data-analyst agent**, which will:
1. Use methodology from **results-analysis skill**
2. Follow academic writing standards
3. Ensure statistical correctness and reproducibility
4. Generate publication-quality outputs

## Notes

- Ensure experiment results include complete statistical information (multiple runs, random seeds)
- Data format should clearly label (column names, units, metric direction)
- For large datasets, analysis may take longer
- Generated Results draft requires manual review and adjustment

## Related Resources

- **Skill**: `results-analysis` - Detailed analysis methodology
- **Agent**: `data-analyst` - Specialized agent for executing analysis
- **References**:
  - `references/statistical-methods.md` - Statistical methods guide
  - `references/results-writing-guide.md` - Results writing standards
  - `references/visualization-best-practices.md` - Visualization best practices

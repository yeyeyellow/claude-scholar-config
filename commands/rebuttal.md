---
name: rebuttal
description: Start systematic review response workflow for professional rebuttal writing
args:
  review_file:
    description: Path to review comments file (optional)
    required: false
---

# /rebuttal - Review Response Workflow

Launch systematic rebuttal writing workflow from review comment analysis to final rebuttal document generation.

## Usage

```bash
/rebuttal [review_file]
```

**Arguments**:
- `review_file` (optional): Path to file containing review comments
  - If not provided, will prompt user to provide review comments

## Features

This command launches complete rebuttal writing workflow:

1. **Get Review Comments** - Read or receive review comments
2. **Analyze and Classify** - Classify comments as Major/Minor/Typo/Misunderstanding
3. **Develop Strategy** - Choose response strategy for each comment
4. **Write Rebuttal** - Generate structured response document
5. **Optimize Tone** - Ensure professional, polite expression
6. **Generate Output** - Save final rebuttal document

## Workflow

### Step 1: Get Review Comments

If `review_file` argument provided:
- Read file content
- Identify number of reviewers and comment structure

If no file provided:
- Guide user to paste or describe review comments
- Confirm number of reviewers

### Step 2: Analyze and Classify

Use `rebuttal-writer` agent for analysis:
- Group comments by reviewer
- Classify as Major/Minor/Typo/Misunderstanding
- Identify priorities

### Step 3: Develop Response Strategy

Choose strategy for each comment:
- **Accept** - Accept and improve
- **Defend** - Politely defend
- **Clarify** - Clear misunderstanding
- **Experiment** - Add experiments

### Step 4: Write Rebuttal

Generate structured responses:
- Write Response and Changes for each comment
- Include specific location references
- Provide evidence and reasoning

### Step 5: Optimize Tone

Check and optimize tone:
- Ensure each response starts with thanks
- Avoid defensive or aggressive language
- Maintain professionalism and respect

### Step 6: Generate Output

Save final documents:
- `rebuttal.md` - Complete rebuttal document
- `review-analysis.md` - Review comment analysis (optional)
- `experiment-plan.md` - Supplementary experiment plan (if needed)

## Output Files

After executing this command, the following files will be generated:

### rebuttal.md
Complete rebuttal document containing:
- Opening (thank reviewers)
- Item-by-item responses (Response + Changes)
- Summary of major changes

### review-analysis.md (optional)
Review comment analysis document containing:
- Comment classification statistics
- Strategy selection rationale
- List of needed supplementary experiments

### experiment-plan.md (optional)
Supplementary experiment plan document containing:
- List of needed experiments
- Purpose and expected results for each experiment
- Experiment priority and time estimate

## Usage Examples

### Example 1: Provide Review File

```bash
/rebuttal reviews.txt
```

Will read review comments from `reviews.txt` file and launch rebuttal writing workflow.

### Example 2: Interactive Input

```bash
/rebuttal
```

Will guide you to paste or describe review comments, then launch rebuttal writing workflow.

## Notes

### Important Principles

1. **No code parsing** - Review comment analysis done via natural language understanding, no automated scripts
2. **Maintain professional tone** - All responses should be polite, respectful, well-reasoned
3. **Provide specific evidence** - Each response should include specific location references and evidence
4. **Completeness check** - Ensure all review comments receive responses

### Reference Resources

This command automatically uses following reference documents:
- `review-classification.md` - Comment classification standards
- `response-strategies.md` - Response strategy guidelines
- `rebuttal-templates.md` - Response template library
- `tone-guidelines.md` - Tone optimization guidelines

### Agent Calls

This command automatically calls `rebuttal-writer` agent to execute rebuttal writing tasks.

## Related Commands

- `/commit` - Submit revised paper
- `/code-review` - Review code quality

---

**Tip**: Before using this command, prepare review comments file and ensure necessary paper modifications are completed.

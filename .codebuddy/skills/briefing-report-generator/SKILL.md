---
name: briefing-report-generator
description: "Generate research briefing reports from analyzed papers. Trigger: '生成今天的 arXiv 研究简报', '帮我汇总这几篇论文的对比表格', '生成研究简报'"
author: Shengyi-Chung
version: 1.0.0
tags:
  - productivity
  - report-automation
  - markdown
  - visualization
---

# briefing-report-generator

## Role
A specialized Skill that transforms analyzed academic data into user-readable, high-quality research briefings with structured reports and comparison tables.

## Input/Output Specifications

### Input
Analyzed Paper List from Member B containing:
- `title`: Paper title
- `key_contributions`: List of main contributions
- `methods`: List of methodologies used
- `novelty_score`: Integer 1-10
- `brief_summary`: Short summary string
- `authors`: Author list
- `published`: Publication date

### Output
1. **Briefing Report**: Markdown-formatted report with:
   - Core Findings section
   - Methodology Summary section
   - Relevance Ranking section

2. **Comparison Table**: Markdown table with:
   - Paper titles
   - Novelty scores
   - Key methods
   - Main contributions

3. **Visualization**: Performance plot showing novelty score distribution

## Workflow

### Step 1: Receive Analyzed Data
Accept the analyzed paper list from Member B (paper-analyzer).

### Step 2: Sort and Filter
- Sort papers by novelty_score (descending)
- Filter by user-specified criteria if applicable
- Group by methodology if multiple papers share similar approaches

### Step 3: Generate Summary Table
Create a Markdown comparison table:

```markdown
| Paper | Novelty | Methods | Key Contributions |
|-------|---------|---------|-------------------|
| Title | 9/10    | GNN, Attention | Novel aggregation |
```

### Step 4: Compose Briefing Report
Structure the report in this order:

```markdown
# Daily arXiv Research Briefing
**Date**: YYYY-MM-DD
**Topic**: [user query]

## Core Findings
- Top 3 most novel papers with brief descriptions
- Emerging trends identified

## Methodology Summary
- Overview of techniques used
- Distribution of approaches

## Relevance Ranking
Ranked list of papers by novelty score with highlights

## Comparison Table
[Insert generated table]
```

### Step 5: Generate Visualization
Create a novelty score distribution chart:

```
Novelty Distribution (10 papers)
★★★★★★★★★★ (9-10): ██ 2 papers
★★★★★★★★☆  (7-8):  ████ 4 papers
★★★★★☆☆    (5-6):  ████ 4 papers
```

ASCII chart or export to `/data/briefing-chart.png` if visualization library available.

### Step 6: Save Report
Store outputs to data layer:

```
/data/
├── briefing-report.md    # Full Markdown report
├── comparison-table.md   # Standalone comparison table
└── briefing-chart.png   # Novelty distribution visualization
```

## Trigger Scenarios

**Automatic Flow:**
- Automatically triggered after Member B completes content analysis

**Specific Queries:**
- "生成今天的 arXiv 研究简报。"
- "请帮我汇总这几篇论文的对比表格。"
- "生成关于图神经网络的研究简报。"

## Error Handling

| Error | Handling |
|-------|----------|
| Empty input from Member B | Request Member B to complete analysis first |
| Missing novelty_score | Default to 5 and note in report |
| Invalid paper data | Skip invalid entries, report count |

## Evaluation Metrics

- **Semantic Coherence**: Report flows logically
- **Information Compression**: Efficient summary vs original abstracts
- **Markdown Compatibility**: Clean rendering in all viewers
- **Visual Clarity**: Chart effectively communicates score distribution

## Integration with Other Skills

- **Input Source**: `paper-analyzer` (Member B)
- **Previous Member**: `paper-search` (Member A) → provides raw data
- **Output**: Final user-facing deliverable

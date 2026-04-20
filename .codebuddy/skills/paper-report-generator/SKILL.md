---
name: paper-report-generator
description: "Generate research reports from analyzed papers. Trigger: 'generate a research report', 'create a report from these papers', 'summarize the papers into a report', 'generate report'"
author: Shengyi-Chung
version: 1.0.0
tags:
  - productivity
  - report-automation
  - markdown
  - summarization
---

# paper-report-generator

## Role
A specialized Skill that transforms analyzed paper data into structured, user-readable research reports with relevance-based highlighting and comprehensive summaries.

## Input/Output Specifications

### Input
Two data sources from upstream Skills:
1. **paper-analysis.json** (from paper-analyze):
   - `analysis_date`: Analysis timestamp
   - `query`: Original search query
   - `total_papers`: Number of papers
   - `ranked_papers`: Array of paper objects with `rank`, `title`, `arxiv_id`, `authors`, `relevance_score`, `key_contributions`, `methods`

2. **papers.json** (from paper-search):
   - Paper metadata including full `abstract` text

### Output

**1. paper_abstracts.json**
```json
{
  "generated_at": "ISO timestamp",
  "total_abstracts": 20,
  "abstracts": [
    {
      "arxiv_id": "2604.xxxxx",
      "title": "Paper Title",
      "authors": ["Author 1", "Author 2"],
      "abstract": "Full abstract text..."
    }
  ]
}
```

**2. paper_reports.md**
Markdown report with:
- Executive Summary section
- **Highlighted High-Relevance Papers** (relevance_score >= 8) at the top
- Detailed sections organized by methodology/topic
- Comparison table
- Full paper list with abstracts

## Workflow

### Step 1: Read Input Data
Read both input files:
- `/data/papers.json` - Contains paper metadata with abstracts
- `/data/paper_analysis.json` - Contains ranked analysis results

### Step 2: Extract Abstracts
Extract abstracts from papers.json and organize into paper_abstracts.json:
```json
{
  "generated_at": "current timestamp",
  "total_abstracts": "count",
  "abstracts": [...]
}
```

### Step 3: Identify High-Relevance Papers
Filter papers with `relevance_score >= 8` as high-relevance candidates.

### Step 4: Generate Report Structure
Build the report in this order:

```markdown
# Research Report: [Topic/Query]

**Generated**: YYYY-MM-DD  
**Total Papers**: N  
**High-Relevance Papers**: M

---

## Executive Summary
Brief overview of the research landscape.

---

## ⭐ High-Relevance Papers (Top Picks)

> **[Paper Title](https://arxiv.org/abs/xxxxx)**  
> Relevance: X/10 | Authors: A, B, C
> 
> **Abstract**: ...
> 
> **Key Contributions**: 
> - Contribution 1
> - Contribution 2
> 
> **Methods**: Method 1, Method 2

[Repeat for each high-relevance paper...]

---

## Detailed Analysis by Category

### Segmentation & Detection
[Medium and low relevance papers grouped by methodology]

### Vision & Language
[Papers related to VLMs, multimodal understanding]

### Benchmarks & Evaluation
[Benchmark papers grouped together]

---

## Comparison Table

| Rank | Paper | Relevance | Key Methods | Main Contribution |
|------|-------|-----------|-------------|------------------|
| 1 | Title | 9/10 | GNN | Novel aggregation |
```

### Step 5: Highlight High-Relevance Papers
- Insert **⭐ High-Relevance Papers** section at the very top (after Executive Summary)
- Use blockquote formatting for emphasis
- Include full abstract for high-relevance papers
- Use **bold** for paper titles and key metrics

### Step 6: Save Outputs
Store outputs to data layer:

```
/data/
├── papers.json              # From paper-search
├── paper_analysis.json      # From paper-analyze
├── paper_abstracts.json     # Extracted abstracts (THIS SKILL)
└── paper_reports.md         # Final report (THIS SKILL)
```

## Trigger Scenarios

**Automatic Flow:**
- Triggered after paper-analyze completes analysis
- User requests report generation

**Specific Queries:**
- "generate a research report"
- "create a report from these papers"
- "summarize the papers into a report"
- "generate report"

## Error Handling

| Error | Handling |
|-------|----------|
| paper_analysis.json not found | Request paper-analyze to run first |
| paper_analysis.json empty | Inform user and suggest new search |
| papers.json missing abstracts | Use truncated abstract or note unavailable |
| Invalid JSON structure | Skip invalid entries, report count |

## Evaluation Metrics

- **Relevance Accuracy**: High-relevance papers correctly identified (score >= 8)
- **Structural Clarity**: Report sections logically organized
- **Information Completeness**: All papers included with abstracts
- **Markdown Compatibility**: Clean rendering in all viewers

## Integration with Other Skills

- **Input Sources**: 
  - `paper-search` (Member A) → `/data/papers.json`
  - `paper-analyze` (Member B) → `/data/paper_analysis.json`
- **Output**: Final user-facing research report → `/data/paper_reports.md`

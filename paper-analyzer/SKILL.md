---
name: paper-analyzer
description: "Deep semantic analysis for arXiv papers. Trigger: '总结这篇论文的主要贡献', '这周哪些论文提出了全新的算法模型？', '分析论文的新颖度和创新点'"
author: Shengyi-Chung
version: 1.0.0
tags:
  - nlp
  - summarization
  - information-extraction
  - llm
---

# paper-analyzer

## Role
A specialized Skill that performs deep semantic analysis on academic papers, extracting key contributions, methodologies, and novelty scores.

## Input/Output Specifications

### Input
Paper Metadata List from Member A containing:
- `title`: Paper title
- `authors`: Author list
- `abstract`: Paper abstract
- `arxiv_id`: ArXiv identifier
- `categories`: Paper categories

### Output
Analyzed Paper List with new fields:
- `key_contributions`: List of main contributions
- `methods`: List of methodologies used
- `novelty_score`: Integer 1-10
- `brief_summary`: Short summary string

## Workflow

### Step 1: Receive Input
Accept the Paper Metadata List from the upstream skill (Member A: arxiv-network-builder or similar data collector).

### Step 2: LLM-Based Analysis
Use an LLM to analyze each paper's abstract and extract:

**Core Contributions:**
```json
"key_contributions": [
  "Novel graph attention mechanism",
  "Scalable training approach for large graphs"
]
```

**Methodologies:**
```json
"methods": [
  "Graph Attention Networks",
  "Self-supervised learning",
  "Transformer architecture"
]
```

### Step 3: Novelty Scoring
Calculate novelty score (1-10) based on:
- Originality of the proposed method
- Novel applications of existing techniques
- Performance improvements over baselines
- Uniqueness in the research domain

**Scoring Guidelines:**
| Score | Description |
|-------|-------------|
| 9-10 | Paradigm shift, entirely new approach |
| 7-8 | Significant novel contributions |
| 5-6 | Solid incremental improvement |
| 3-4 | Minor variations on existing work |
| 1-2 | Largely derivative work |

### Step 4: Generate Brief Summary
Create a concise summary (2-3 sentences) capturing:
- What problem is addressed
- What approach is proposed
- Key results achieved

### Step 5: Output to Data Layer
Store the analyzed results in structured format:

```
/data/
├── analyzed_papers.json  # Full analyzed dataset
└── top_novel_papers.json # Filtered by novelty_score >= 8
```

## Trigger Scenarios

**Automatic Flow:**
- Triggered automatically after Member A completes paper retrieval in the "Daily arXiv Briefing" workflow

**Specific Queries:**
- "总结这篇关于图神经网络论文的主要贡献。"
- "这周哪些论文提出了全新的算法模型？"
- "分析这批论文的新颖度和创新点。"

## Error Handling

| Error | Handling |
|-------|----------|
| LLM API timeout | Retry once, then skip paper with error flag |
| Empty abstract | Mark as "analysis_failed" with reason |
| Invalid metadata | Skip invalid entries, log errors |
| Rate limit | Wait and retry with exponential backoff |

## Integration with Other Skills

- **Input Source**: `arxiv-network-builder` or any skill providing paper metadata
- **Output Consumer**: Skills generating summary tables, reports, or visualizations

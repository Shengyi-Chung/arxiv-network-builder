---
name: paper-search
description: "Build social networks from arXiv papers. Trigger: '查找关于 [关键词] 的最新论文', '分析 [领域] 的学者关系', '搜索本周关于 [主题] 的新论文', '构建 [领域] 的合作网络图'"
author: Shengyi-Chung
version: 1.0.0
tags:
  - social-network-analysis
  - data-mining
  - arxiv
  - graph-construction
---

# arxiv-network-builder

## Role
A specialized Skill that automates arXiv paper retrieval via arXiv API and constructs social network graphs from author collaborations.

## Workflow

### Step 1: Parse User Query
Extract the search keywords from the user's request. Identify the target research domain or topic.

**Examples:**
- "深度学习" → search query: "deep learning"
- "图神经网络" → search query: "graph neural network"

### Step 2: Query arXiv API
Use the arXiv API to fetch papers matching the query.

**API Endpoint:** `http://export.arxiv.org/api/query`

**Parameters:**
- `search_query`: The extracted keyword(s)
- `start`: Pagination offset
- `max_results`: Number of papers to retrieve (default: 50)
- `sortBy`: "submittedDate" for latest papers

### Step 3: Extract Metadata
Parse the XML response to extract:
- Paper ID
- Title
- Abstract
- Author names
- Published date
- Categories

### Step 4: Build Network Graph

**Nodes (Authors):**
```json
{
  "id": "author_name",
  "papers": ["paper_id_1", "paper_id_2"],
  "paper_count": 2
}
```

**Edges (Co-authorship):**
```json
{
  "source": "author_a",
  "target": "author_b",
  "paper_id": "paper_id",
  "weight": 1
}
```

### Step 5: Save to Data Layer
Store the structured network data as JSON files:

```
/data/
├── nodes.json    # Author nodes
├── edges.json    # Co-authorship edges
└── papers.json   # Paper metadata
```

## Error Handling

| Error | Handling |
|-------|----------|
| No papers found | Inform user and suggest broader keywords |
| API timeout | Retry once, then report failure |
| Invalid query | Ask user to rephrase the search terms |
| Too many results | Limit to 100 papers and notify user |

## Output Format

The Skill outputs structured JSON data ready for downstream Skills:
- **Ranking Skills**: Can use node/edge data for centrality analysis
- **Visualization Skills**: Can render the network graph

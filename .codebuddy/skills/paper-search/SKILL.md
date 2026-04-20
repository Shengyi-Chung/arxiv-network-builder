---
name: paper-search
description: "Search arXiv papers and build author collaboration networks. Trigger: 'search for papers about [keyword]', 'find latest papers on [topic]', 'build a collaboration network for [domain]', 'search [N] papers about [topic]'"
author: Shengyi-Chung
version: 1.0.0
tags:
  - social-network-analysis
  - data-mining
  - arxiv
  - graph-construction
---

# paper-search

## Role
A specialized Skill that automates arXiv paper retrieval via arXiv API and constructs social network graphs from author collaborations.

## Workflow

### Step 1: Parse User Query
Extract the search keywords from the user's request. Identify the target research domain or topic.

**Examples:**
- "deep learning" → search query: "deep learning"
- "graph neural network" → search query: "graph neural network"

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
| API timeout | Retry once with exponential backoff, then report failure |
| Invalid query | Ask user to rephrase the search terms |
| Too many results | Limit to 100 papers and notify user |
| Rate limit (429) | Wait 60 seconds, use exponential backoff |

## Performance Optimization

### Batch Processing
- Group queries by topic/domain when possible
- Use arXiv API's `max_results` parameter to control batch size (recommended: 50 per request)
- Prioritize recent papers by setting `sortBy=submittedDate`

### Concurrent Requests
- When querying multiple topics, make requests sequentially to avoid rate limiting
- Wait at least **3 seconds** between each API call

### Caching Strategy
- Cache search results for the same query for 24 hours
- Store in `/data/cache/` directory with query hash as filename
- Skip API call if valid cache exists

### Rate Limiting
- arXiv API allows ~3 requests per 5 minutes
- If rate limited (429 response), wait 60 seconds and retry
- Log rate limit events for monitoring

## Output Format

The Skill outputs structured JSON data ready for downstream Skills:
- **Ranking Skills**: Can use node/edge data for centrality analysis
- **Visualization Skills**: Can render the network graph

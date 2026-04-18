---
name: query-holder
description: "Manage follow-up queries about retrieved papers. Trigger: 'tell me more about [paper title]', 'show papers related to [keyword]', 'compare these two papers', 'find other papers by [author]'"
author: Shengyi-Chung
version: 1.0.0
tags:
  - retrieval
  - context-management
  - nlp
  - conversation
---

# query-holder

## Role
A specialized Skill that manages follow-up conversational context between the user and retrieved paper data. Acts as a "local knowledge base" interface for querying existing structured data after initial retrieval by Member A and analysis by Member B.

## Input/Output Specifications

### Input

**1. Structured Paper Data** (from paper-analyze):
- Location: `/data/paper_analysis.json`
- Contains: `ranked_papers` array with `title`, `arxiv_id`, `authors`, `relevance_score`, `key_contributions`, `methods`

**2. User Natural Language Query**:
- Follow-up questions in natural language
- Examples: specific paper inquiries, keyword filtering, comparison requests

### Output

**1. Filtered Results**:
```json
{
  "query_type": "paper_detail|keyword_filter|comparison|author_search",
  "matched_papers": [...],
  "answer": "Natural language response",
  "suggested_action": "direct_answer|generate_report|none"
}
```

**2. Console Output**:
- Direct answers to user queries
- Highlighted paper details
- Comparison summaries

## Query Types

### Type 1: Paper Detail Query
**Trigger**: "tell me more about [paper title]"
**Action**: Retrieve full paper info and provide detailed summary

### Type 2: Keyword Filter Query
**Trigger**: "show papers related to [keyword]"
**Action**: Filter papers by keyword match in title/abstract/contributions

### Type 3: Comparison Query
**Trigger**: "compare [paper A] and [paper B]"
**Action**: Extract and compare methods/contributions from two papers

### Type 4: Author Search Query
**Trigger**: "find other papers by [author name]"
**Action**: Search all papers by specified author

## Workflow

### Step 1: Parse User Query
Identify query type and extract key entities:
- Paper titles mentioned
- Author names
- Keywords for filtering
- Comparison targets

### Step 2: Load Existing Data
Read from `/data/paper_analysis.json`:
```json
{
  "analysis_date": "...",
  "query": "original search query",
  "ranked_papers": [...]
}
```

### Step 3: Intent Classification
Classify query into one of:
| Type | Keywords | Example |
|------|----------|---------|
| paper_detail | "more details", "tell me more", "details" | "tell me more about SegWithU" |
| keyword_filter | "related to", "about", "involving" | "show papers related to uncertainty" |
| comparison | "compare", "difference", "versus" | "compare these two papers" |
| author_search | "author", "by", "other papers" | "find other papers by Tianhao Fu" |

### Step 4: Retrieve Matching Papers
Filter papers based on query type:
- **paper_detail**: Match by title (fuzzy match)
- **keyword_filter**: Match by keyword in title/abstract/contributions
- **comparison**: Match both papers by titles
- **author_search**: Match by author name

### Step 5: Generate Response
Provide contextual answer:
```
**Query**: [user query]
**Matched Papers**: [count]

[Detailed response based on query type]
```

### Step 6: Suggest Next Action
Based on matched papers count:
| Count | Suggested Action |
|-------|------------------|
| 1-2 | direct_answer (provide detail) |
| 3+ | generate_report (update report) |
| 0 | none (no match found) |

## Trigger Scenarios

**Follow-up Queries:**
- "tell me more about SegWithU"
- "show papers related to uncertainty"
- "compare SegWithU and Unsupervised Skeleton-Based Action Segmentation"
- "find other papers by Tianhao Fu"

**Contextual Questions:**
- "what are the main contributions of this paper?"
- "which papers use hierarchical methods?"
- "which paper has the highest novelty?"

## Error Handling

| Error | Handling |
|-------|----------|
| paper_analysis.json not found | Inform user to run paper-search and paper-analyze first |
| No papers match query | Return empty results with suggestions for broader query |
| Ambiguous paper title | Return top 3 matches and ask user to clarify |
| Query type unrecognized | Default to keyword filter search |

## Evaluation Metrics

- **Retrieval Accuracy**: Match between user query intent and returned paper subset
- **Context Coherence**: Correctly associates papers with previous conversation context
- **Response Quality**: Clear, concise answers with relevant paper details
- **Action Suggestion**: Appropriate next-step recommendations

## Integration with Other Skills

- **Input Source**: `paper-analyze` (Member B) → `/data/paper_analysis.json`
- **Data Layer**: `/data/` (shared knowledge base)
- **Output Consumer**: Can trigger `paper-report-generator` (Member C) for subset reports

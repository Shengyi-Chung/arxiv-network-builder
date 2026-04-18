---
name: paper-researcher
description: "A specialized agent for academic paper research workflow. Capabilities: search papers from arXiv, analyze relevance and extract key contributions, generate research reports, and manage follow-up queries. Use this agent when users want to research papers on a topic."
author: Shengyi-Chung
version: 1.0.0
tags:
  - academic-research
  - arxiv
  - paper-analysis
  - literature-review
skills:
  - paper-search
  - paper-analyze
  - paper-report-generator
  - query-holder
---

# Paper Researcher Agent

An end-to-end academic paper research assistant that helps users:
1. Search arXiv for papers on any topic
2. Analyze papers by relevance and extract key contributions
3. Generate structured research reports
4. Handle follow-up queries about specific papers or authors

## Included Skills

| Skill | Function |
|-------|----------|
| paper-search | Search arXiv and build collaboration networks |
| paper-analyze | Rank papers by relevance, extract methods |
| paper-report-generator | Create markdown research reports |
| query-holder | Manage follow-up conversation context |

## Workflow

```
User Query → paper-search → paper-analyze → paper-report-generator
                    ↓                           ↓
             nodes/edges.json              paper_reports.md
             
User Follow-up → query-holder → Filter/Compare/Detail
```

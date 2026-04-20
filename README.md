# Paper Researcher Agent

A specialized agent for academic paper research workflow.

## Capabilities

- **Search papers** from arXiv on any topic
- **Analyze relevance** and extract key contributions
- **Generate research reports** in structured format
- **Manage follow-up queries** about specific papers or authors

## Included Skills

| Skill | Function | Trigger |
|-------|----------|---------|
| `paper-search` | Search arXiv and build collaboration networks | "search papers about X", "find latest papers on X" |
| `paper-analyze` | Rank papers by relevance, extract methods | "analyze these papers", "rank by relevance" |
| `paper-report-generator` | Create markdown research reports | "generate research report" |
| `briefing-report-generator` | Generate briefing reports | "generate briefing", "summarize findings" |
| `query-holder` | Manage follow-up conversation context | "tell me more about X", "compare these papers" |

## Workflow

```
User Query → paper-search → paper-analyze → paper-report-generator
                    ↓                           ↓
             nodes/edges.json              paper_reports.md
             
User Follow-up → query-holder → Filter/Compare/Detail
```

## Usage

Use this agent when you want to:
- Research papers on a specific topic
- Find latest publications in a field
- Build author collaboration networks
- Generate literature review reports
- Compare multiple papers

## Structure

```
paper-researcher/
├── AGENTS.md              # Agent definition
├── README.md              # This file
└── .codebuddy/skills/     # Skills directory
    ├── paper-search/
    ├── paper-analyze/
    ├── paper-report-generator/
    ├── briefing-report-generator/
    └── query-holder/
```

## Author

Shengyi-Chung

## Version

1.0.0

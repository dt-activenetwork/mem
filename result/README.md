# Task Outputs / Results

**Purpose**: Store final deliverables produced by task execution.

**Directory structure**:
```
/result/
├── 1/              # Task 1 outputs
│   ├── doc1.md
│   └── doc2.md
├── 2/              # Task 2 outputs
│   ├── analysis.md
│   └── report.md
└── 3/              # Task 3 outputs
    └── presentation.md
```

**What goes here**:
- ✅ Final documentation (analysis, reports, presentations)
- ✅ Generated diagrams and visualizations
- ✅ Code analysis results
- ✅ Any deliverable meant for end users

**What does NOT go here**:
- ❌ Intermediate drafts (goes to `/temp/`)
- ❌ Memory notes (goes to `/memory/`)
- ❌ Temporary working files

**Naming convention**:
- Use task numbers for organization: `/result/<task_number>/`
- Use descriptive names for files: `analysis.md`, `architecture-overview.md`
- Prefer Markdown format (`.md`) for text documents

**Integration with memory system**:
- Every output file should be referenced in an episodic note
- Episodic notes link to outputs via `outputs:` field in frontmatter
- Optional: Add backlink in output file pointing to episodic note

**Example backlink** (in output file):
```markdown
<!-- This document was created as part of Task 3 -->
<!-- See memory note: /memory/episodic/20251028-t3-presentation.md -->
```

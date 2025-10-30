# Memory-Driven Context Optimization System

**A Universal Architecture for AI Agents in Research & Retrieval Scenarios**

---

## Document Status

- **Version**: 2.0.0
- **Last Updated**: 2025-10-30
- **Origin**: Extracted from CPG project (read branch)
- **Scope**: Universal design applicable to any research/retrieval scenario
- **License**: Open for adaptation and reuse

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [The Context Problem](#the-context-problem)
3. [Core Architecture](#core-architecture)
4. [Memory Types](#memory-types)
5. [Index System](#index-system)
6. [The Memory-First Workflow](#the-memory-first-workflow)
7. [Incremental Work Pattern](#incremental-work-pattern)
8. [Performance Metrics](#performance-metrics)
9. [Implementation Guide](#implementation-guide)
10. [Universal Application Examples](#universal-application-examples)

---

## Executive Summary

### The Problem

AI agents working with large codebases, documentation sets, or research materials face a fundamental challenge: **context window limitations**. Reading all relevant materials for each task leads to:

- 🚨 Context overflow (exceeding model limits)
- 🚨 Inefficient token usage (re-reading the same content)
- 🚨 Loss of continuity (forgetting previous analysis)
- 🚨 Inconsistent understanding (deriving facts differently each time)

### The Solution

A **file-based external memory system** that enables AI agents to:

- ✅ Store knowledge persistently across sessions
- ✅ Retrieve information efficiently through indexes
- ✅ Build on previous work (not start from scratch)
- ✅ Maintain consistency (memory as source of truth)
- ✅ Optimize context usage (80-95% reduction)

### Key Results

| Scenario | Without Memory | With Memory | Reduction |
|----------|----------------|-------------|-----------|
| Session start | 8300 lines (read all docs) | 500 lines (indexes + recent notes) | **94%** |
| Answer question | 5300 lines (full docs) | 500 lines (targeted notes) | **91%** |
| Large task execution | 9500 lines (all materials) | 1200 lines (indexed sections) | **87%** |

---

## The Context Problem

### Traditional Approach

```
User: "Analyze this codebase and create documentation"

AI Agent Workflow:
1. Read 50 source files (10,000 lines)
2. Analyze and extract patterns
3. Write documentation
4. Session ends, context lost

Next Session:
User: "Add more details to section 3"
Agent: Must re-read same 50 files (10,000 lines again)
       Re-analyze patterns (redundant work)
       Update documentation
```

**Problems**:
- Re-reading same content wastes tokens
- Re-analysis may produce inconsistent results
- No learning or knowledge accumulation
- Context window quickly fills up

### Memory-Driven Approach

```
User: "Analyze this codebase and create documentation"

AI Agent Workflow:
1. Check memory: Has anyone (me, in past) analyzed this?
   → Yes: Read semantic notes (500 lines) instead of code (10,000 lines)
2. Use memory knowledge as foundation
3. Write documentation
4. Update memory with new insights
5. Session ends

Next Session:
User: "Add more details to section 3"
Agent: Read memory note for section 3 (100 lines)
       Read semantic notes for related concepts (200 lines)
       Update documentation
       Update memory note
```

**Benefits**:
- No redundant reading (90% context savings)
- Consistent understanding (memory as single source of truth)
- Incremental learning (each session adds to knowledge base)
- Context budget available for real work

---

## Core Architecture

### Three-Layer Design

```
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                         │
│  AI Agent using memory for tasks (analysis, documentation)  │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│                     INDEX LAYER                              │
│  • Global Index (tags.json, topics.json)                    │
│  • Task-Specific Index (task-N-index.json)                  │
│  • Quick discovery without reading full content             │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│                    STORAGE LAYER                             │
│  • Semantic Memory (stable facts, architecture)             │
│  • Episodic Memory (session records, experiments)           │
│  • Procedural Memory (workflows, checklists)                │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow

**Read Path** (Retrieve knowledge):
```
User Query
  → Check Task-Specific Index (if exists)
  → Query Global Index (tags/topics)
  → Identify relevant note IDs
  → Read specific notes (with line ranges if indexed)
  → Return knowledge to agent
```

**Write Path** (Capture knowledge):
```
New Knowledge Discovered
  → Create/Update Memory Note (semantic/episodic/procedural)
  → Extract tags and topics
  → Update Global Index
  → Add cross-references to related notes
  → Optional: Update task-specific index
```

---

## Memory Types

### 1. Semantic Memory (Stable Knowledge)

**Purpose**: Store facts, definitions, and architecture that don't change often.

**When to create**:
- Discovered architectural pattern
- Understood core component design
- Identified design principles or conventions
- Found important API contracts

**Structure**:
```markdown
---
id: sem-NNN
title: <Concept Name>
type: semantic
tags: [tag1, tag2, tag3]
created: YYYY-MM-DD
updated: YYYY-MM-DD
source: <origin of knowledge>
related: [sem-XXX, ep-YYY]
---

# <Title>

## Why now
<1-2 sentences: why captured at this time>

## Core Concept
<Definition and high-level explanation>

## Key Features
<Detailed breakdown with evidence>

## Capabilities / Limitations
<What it can/cannot do>

## Evidence
<Links to source with file:line references>

## Cross-References
<Links to related notes>
```

**Best Practices**:
- ✅ One idea per note (don't mix concepts)
- ✅ Evidence-based (quote sources with paths and lines)
- ✅ Update in place (when facts change, update note)
- ✅ Cross-link liberally (connect related concepts)

**Examples**:
- `database-schema-design.md` (stable architecture)
- `authentication-flow.md` (core mechanism)
- `api-rate-limiting.md` (system capability)

---

### 2. Episodic Memory (Session Records)

**Purpose**: Record what was done during a work session.

**When to create**:
- Completed significant analysis
- Finished task or major phase
- Created output files
- Encountered and resolved issues

**Structure**:
```markdown
---
id: ep-NNN
title: <YYYYMMDD>-<task>-<description>
type: episodic
date: YYYY-MM-DD
task: <task-name>
tags: [task-completion, ...]
outputs: [list of files created]
related: [sem-XXX, proc-YYY]
---

# <Task Title>

## Goal
<What was the objective?>

## Approach
<What strategy was used?>

## Steps Taken
1. Step 1
2. Step 2
...

## Key Findings
- Finding 1 (with evidence)
- Finding 2 (with evidence)

## Outputs Produced
- /path/to/output1.md
- /path/to/output2.md

## Semantic Notes Created/Updated
- sem-XXX: <what was added>
- sem-YYY: <what was updated>

## Next Steps / Open Questions
- TODO 1
- TODO 2
```

**Best Practices**:
- ✅ Create at task completion (not days later)
- ✅ Link to all output files
- ✅ Extract semantic insights (promote to semantic notes)
- ✅ Document mistakes (what didn't work and why)

**Examples**:
- `20251028-database-migration-analysis.md`
- `20251029-api-performance-testing.md`
- `20251030-security-audit-phase1.md`

---

### 3. Procedural Memory (Workflows)

**Purpose**: Codify reusable workflows and checklists.

**When to create**:
- Completed a workflow more than twice
- Identified repeating pattern of steps
- User requests formal procedure

**Structure**:
```markdown
---
id: proc-NNN
title: Workflow for <Activity>
type: procedural
tags: [workflow, domain]
created: YYYY-MM-DD
updated: YYYY-MM-DD
domain: <e.g., code-analysis>
verb: <e.g., analyze-and-document>
related: [ep-XXX]
---

# <Title>

## Purpose
<Why this workflow exists>

## Prerequisites
- Prerequisite 1
- Prerequisite 2

## Steps
1. **Step 1**: <Action>
   - Detail 1
   - Detail 2

2. **Step 2**: <Action>
   - Detail 1
   - Detail 2

## Checks / Validation
- [ ] Check 1
- [ ] Check 2

## Common Pitfalls
- Pitfall 1: <What to avoid and why>
- Pitfall 2: <What to avoid and why>

## Examples
- Example 1: <Link to ep-XXX where workflow was used>
```

**Best Practices**:
- ✅ Refine iteratively (update based on experience)
- ✅ Link to examples (reference episodic notes)
- ✅ Include failure modes (what can go wrong)

**Examples**:
- `database-schema-analysis-workflow.md`
- `api-documentation-creation-workflow.md`
- `security-audit-checklist.md`

---

## Index System

### Global Index (Discovery)

**Purpose**: Enable fast discovery of notes by keyword or subject area.

**tags.json Schema**:
```json
{
  "tags": {
    "database": ["sem-001", "sem-005", "ep-002"],
    "authentication": ["sem-003", "ep-001"],
    "api": ["sem-002", "sem-004", "ep-003"],
    "performance": ["sem-006", "ep-004"]
  },
  "updated": "YYYY-MM-DD"
}
```

**topics.json Schema**:
```json
{
  "topics": {
    "System Architecture": {
      "description": "High-level system design and components",
      "notes": ["sem-001", "sem-002", "ep-001"]
    },
    "Authentication & Security": {
      "description": "Authentication flows, authorization, security",
      "notes": ["sem-003", "ep-002", "proc-001"]
    }
  },
  "updated": "YYYY-MM-DD"
}
```

**Maintenance**:
- Update **immediately** after creating/updating any note
- Add new tags/topics as discovered
- Normalize tag names (lowercase, hyphen-separated)

---

### Task-Specific Index (Optimization)

**Purpose**: Optimize context usage for large tasks by specifying exact sections to read.

**When to build**:
- Starting NEW task requiring synthesizing knowledge
- Task type is presentation/teaching/documentation
- Existing documentation is large (>3000 lines)
- User explicitly requests context optimization

**Schema**:
```json
{
  "task_id": "N",
  "task_name": "Create presentation on API architecture",
  "task_type": "presentation",
  "created": "YYYY-MM-DD",

  "knowledge_requirements": {
    "critical": {
      "API Architecture Overview": {
        "source": "output/api-documentation.md",
        "note_id": "sem-002",
        "sections_needed": ["lines 20-80", "lines 150-200"],
        "estimated_lines": "~110 lines",
        "summary": "Core API design patterns and endpoints"
      },
      "Authentication Flow": {
        "source": "output/auth-analysis.md",
        "note_id": "sem-003",
        "sections_needed": ["entire note"],
        "estimated_lines": "~250 lines",
        "summary": "How authentication and authorization work"
      }
    },
    "optional": {
      "Performance Considerations": {
        "source": "output/performance-report.md",
        "sections_needed": ["lines 50-100"],
        "estimated_lines": "~50 lines",
        "summary": "Rate limiting and caching strategies"
      }
    }
  },

  "estimated_context_usage": {
    "critical_reading": "~360 lines",
    "total": "~410 lines (vs 3000 lines if reading all)",
    "reduction": "86% reduction"
  }
}
```

**Benefits**:
- **91% context reduction** (reading only needed sections)
- **Faster task completion** (less reading overhead)
- **Focused knowledge** (no noise from irrelevant sections)

---

## The Memory-First Workflow

### Core Principle

**Memory system is your external brain. Consult it BEFORE any action, update it AFTER every significant discovery.**

---

### Three-Phase Mandatory Workflow

#### Phase 1: CONSULT MEMORY (BEFORE work)

```
┌─────────────────────────────────────────────────────────┐
│  MANDATORY CHECKS (Do these FIRST, always)             │
├─────────────────────────────────────────────────────────┤
│  1. Read index files (tags.json, topics.json)          │
│  2. Query for relevant tags/topics based on task       │
│  3. Read identified semantic notes (stable knowledge)  │
│  4. Read recent episodic notes (1-2 most recent)       │
│  5. Check for task-specific index (task-N-index.json)  │
│  6. Read procedural notes if workflow-related          │
└─────────────────────────────────────────────────────────┘
```

**Questions to ask BEFORE any work**:
- ❓ Have I read the index files?
- ❓ Does memory already contain knowledge I need?
- ❓ Did someone (me, in past session) already analyze this?
- ❓ Are there semantic notes for concepts I'm working with?
- ❓ Is there a task-specific index to optimize reading?

**If answer to ANY is "I don't know" → Read memory FIRST**

---

#### Phase 2: WORK WITH MEMORY AS FOUNDATION (DURING work)

**Operating Rules**:

✅ **DO**:
1. Use semantic notes as authoritative knowledge
2. Reference episodic notes for past analysis results
3. Follow procedural notes for workflows
4. Only read NEW materials if memory insufficient
5. Continuously update memory during work (don't batch)

❌ **DON'T**:
1. NEVER re-analyze what's already in memory
2. NEVER ignore existing knowledge
3. NEVER batch memory updates (update immediately)
4. NEVER create duplicate notes (check first)
5. NEVER skip index updates

**Real-Time Memory Updates**:

```
Trigger: Understood new architecture
Action: Create semantic note IMMEDIATELY
Example: After analyzing authentication code → Create sem-003 now

Trigger: User corrected understanding
Action: Update semantic note IMMEDIATELY + create episodic note
Example: User says "API uses OAuth2" → Update sem-002 + create ep-003

Trigger: Completed major analysis phase
Action: Update episodic note with progress
Example: After analyzing 4 modules → Update ep-003 with findings

Trigger: Discovered related concepts
Action: Add cross-references to notes
Example: Auth uses JWT → Add link from sem-003 to sem-005
```

---

#### Phase 3: UPDATE MEMORY (AFTER work)

**Mandatory Updates**:

1. **Create/Update Semantic Notes** (stable knowledge)
   - New architectural pattern discovered
   - Core component design understood
   - Design principles identified

2. **Create/Update Episodic Notes** (session record)
   - Completed significant analysis
   - Finished task or major phase
   - Created output files

3. **Update Index Files** (IMMEDIATELY)
   - Add note ID to all relevant tags in tags.json
   - Add note ID to all relevant topics in topics.json
   - Update timestamp

4. **Add Cross-References**
   - In note frontmatter: `related: [sem-XXX, ep-YYY]`
   - In note body: Link to related concepts

5. **Link Episodic Notes to Output Files**
   - List all output files in episodic note
   - Add backlinks from outputs to episodic note

---

### Memory-First Checklist

**Session Start**:
- [ ] Read tags.json and topics.json
- [ ] Read last 1-2 episodic notes
- [ ] Query and read relevant semantic notes
- [ ] Check for task-specific index
- [ ] Read procedural notes if workflow-related

**During Work**:
- [ ] Using memory as foundation (not re-analyzing)
- [ ] Creating notes continuously (not batching)
- [ ] Updating indexes immediately (not deferring)

**Before Finishing**:
- [ ] Created episodic note documenting session
- [ ] Created/updated semantic notes for insights
- [ ] Updated tags.json and topics.json
- [ ] Added cross-references
- [ ] Linked episodic note to outputs

---

## Incremental Work Pattern

### The Context Overflow Problem

**Challenge**: Large tasks can exceed context limits and lose continuity if interrupted.

**Example**:
```
❌ Monolithic Approach:
Task: "Create 40-slide presentation based on all previous work"
Agent: Reads 9100 lines (all documentation)
       Writes 2000-line presentation in one go
       Updates memory at end

Problems:
- Context window overflow (11,000+ lines)
- Work lost if interrupted mid-task
- No progress tracking
- All-or-nothing execution
```

---

### The Incremental Solution

**Pattern**: Break large tasks into small steps, update memory after EACH step.

```
┌─────────────────────────────────────────┐
│ Step N: Do Work                         │
│ • Read memory (resume from checkpoint)  │
│ • Execute ONE incremental unit          │
│ • Keep context <2000 lines              │
│ • Create/update notes IMMEDIATELY       │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│ Memory Checkpoint                        │
│ • Episodic note records progress        │
│ • Semantic notes capture insights       │
│ • Indexes updated                       │
│ • "Next Steps" documented               │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│ Step N+1: Continue                      │
│ • Read memory (load next context)       │
│ • Next incremental unit                 │
│ ...                                     │
└─────────────────────────────────────────┘
```

---

### When to Use Incremental Approach

**MANDATORY for tasks that**:
- ✅ Require reading >5000 lines of context
- ✅ Involve analyzing >10 files
- ✅ Produce outputs >1000 lines
- ✅ Have multiple distinct phases

**Optional for smaller tasks** (<5000 lines total)

---

### Universal Step Structure

**EVERY incremental step MUST follow this 4-phase pattern**:

#### Phase 1: Checkpoint In (Read Memory)
1. Read episodic note for current task
2. Check "Progress Tracking" section
   - What steps are completed?
   - What is the current step?
3. Read "Context for Next Step"
   - What knowledge is needed?
   - Which semantic notes to load?
4. Load referenced notes
5. Verify: Ready to proceed

#### Phase 2: Do Work (Execute One Unit)
1. Focus on ONE incremental unit
   - Analyze one module group (5-10 files)
   - Write one documentation section
   - Create one presentation slide group
2. Keep context <2000 lines for this step
3. Track discoveries in working notes
4. Complete the unit fully (no partial work)

#### Phase 3: Checkpoint Out (Update Memory)
1. Update episodic note IMMEDIATELY:
   - Mark current step as ✅ complete
   - Add discoveries to "Findings"
   - Update "Progress Tracking"
   - Define "Next Steps"
   - Add "Context for Next Step"
2. Create/update semantic notes (if insights)
3. Update indexes (if new notes)
4. Save intermediate results

#### Phase 4: Verify Continuity
1. Re-read episodic note's "Next Steps"
2. Question: Can next step resume from this?
   - Is context clear?
   - Are references complete?
   - Is progress unambiguous?
3. If YES: Proceed to next step
4. If NO: Fix episodic note before proceeding

---

### Context Budget Per Step

**Hard Limits** (MUST NOT exceed):
- **Single step context**: <2000 lines
- **Total memory notes read**: <1000 lines
- **New materials read**: <1000 lines
- **Output produced**: <500 lines

**Why**: Prevents overflow, ensures digestibility, forces proper decomposition

---

### Task Decomposition Strategies

**By File Groups** (for large material sets):
- Analyze 5-10 related files per step
- Update memory after each group

**By Sections** (for long documentation):
- Write one section (200-400 lines) per step
- Checkpoint after each section

**By Concepts** (for multi-concept analysis):
- Focus on one concept per step
- Create semantic note per concept

**By Phases** (for multi-phase tasks):
- Research → Documentation → Review
- Checkpoint between phases

---

### Checkpoint Format: Episodic Note

```markdown
---
id: ep-NNN
title: YYYYMMDD-task-name
type: episodic
tags: [task-completion, incremental-work]
created: YYYY-MM-DD
updated: YYYY-MM-DD
task: task-name
status: in-progress
---

# Task Name

**Work Mode**: Incremental with checkpoints

## Goal
<Overall objective>

## Approach
<Strategy and decomposition plan>

## Progress Tracking

**Completed Steps**:
1. ✅ [Step 1 description] (YYYY-MM-DD HH:MM)
   - Key findings: ...
   - Context used: ~XXX lines
   - Outputs: file.md (section 1-3)

2. ✅ [Step 2 description] (YYYY-MM-DD HH:MM)
   - Key findings: ...
   - Context used: ~XXX lines
   - Outputs: file.md (section 4-6)

3. 🔄 **Currently**: [Step 3 description] (in progress)
   - Started: YYYY-MM-DD HH:MM
   - Expected context: ~XXX lines

**Pending Steps**:
- [ ] Step 4: [description]
- [ ] Step 5: [description]

## Context for Next Step

**What's needed for Step 4**:
- Memory: sem-003 (concept X)
- Previous outputs: output.md (sections 1-6 complete)
- Focus: Next topic Y

**Key context**:
- Sections 4-6 covered: Topics A, B, C
- Next focus: Topic D and integration with A
- Related: See sem-004 for background

## Findings
<Cumulative findings from all steps>

## Outputs
- **[output.md](output.md)**: Description (Sections 1-6 complete)
```

---

### Checkpoint Verification Checklist

- [ ] Current step marked ✅ complete
- [ ] Next step clearly defined
- [ ] "Context for Next Step" present
- [ ] Progress percentages updated
- [ ] Intermediate results saved
- [ ] Findings updated
- [ ] Timestamp recorded
- [ ] **Resumability test**: "Can I resume if interrupted now?" (YES)

---

## Performance Metrics

### Expected Performance

| Metric | Target | Rationale |
|--------|--------|-----------|
| **Context Reduction** | 80-95% | Reading targeted notes vs all materials |
| **Session Startup** | <60 seconds | Reading indexes + 2-3 notes |
| **Knowledge Reuse** | >70% | % from memory vs new analysis |
| **Index Consistency** | 100% | All notes indexed, no orphans |
| **Note Freshness** | <5 tasks old | Memory stays current |

### Incremental Work Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Step Context** | <2000 lines | Lines read per step |
| **Checkpoint Frequency** | Every 15-30 min | Time between updates |
| **Step Completion** | >90% | % completed without restart |
| **Resume Time** | <3 minutes | Time to resume from checkpoint |
| **Resumability** | 100% | Can resume from any checkpoint |

### Red Flags

- 🚨 Context usage >5000 lines per task
- 🚨 Re-analyzing materials already in memory
- 🚨 Creating notes at end (not continuously)
- 🚨 Orphaned notes (not in index)
- 🚨 Starting work without reading memory
- 🚨 Step context >2000 lines (too large)
- 🚨 No checkpoints >30 min (forgetting updates)

---

## Implementation Guide

### Directory Structure

```
/memory/
├── semantic/                    # Stable knowledge
│   ├── concept-a.md                 (sem-001)
│   ├── concept-b.md                 (sem-002)
│   └── ...
├── episodic/                    # Session records
│   ├── YYYYMMDD-task-1.md          (ep-001)
│   ├── YYYYMMDD-task-2.md          (ep-002)
│   └── ...
├── procedural/                  # Workflows
│   ├── workflow-a.md               (proc-001)
│   └── ...
├── index/                       # Indexes
│   ├── tags.json                   # Global tag index
│   ├── topics.json                 # Global topic index
│   ├── task-1-index.json           # Task-specific (optional)
│   └── ...
└── DESIGN.md                    # Memory system documentation
```

### Step-by-Step Setup

#### Step 1: Create Directory Structure

```bash
mkdir -p memory/{semantic,episodic,procedural,index}
touch memory/index/tags.json
touch memory/index/topics.json
```

#### Step 2: Initialize Index Files

**tags.json**:
```json
{
  "tags": {},
  "updated": "YYYY-MM-DD"
}
```

**topics.json**:
```json
{
  "topics": {},
  "updated": "YYYY-MM-DD"
}
```

#### Step 3: Create First Semantic Note

When you discover your first concept:

```markdown
---
id: sem-001
title: First Concept
type: semantic
tags: [domain, architecture]
created: 2025-10-30
updated: 2025-10-30
source: analysis of X
related: []
---

# First Concept

## Why now
Discovered this while analyzing X, important for understanding Y.

## Core Concept
<Explanation>

## Evidence
- source.txt:123-145
```

#### Step 4: Update Indexes

After creating sem-001, update **tags.json**:
```json
{
  "tags": {
    "domain": ["sem-001"],
    "architecture": ["sem-001"]
  },
  "updated": "2025-10-30"
}
```

Update **topics.json**:
```json
{
  "topics": {
    "System Architecture": {
      "description": "High-level design",
      "notes": ["sem-001"]
    }
  },
  "updated": "2025-10-30"
}
```

#### Step 5: Create First Episodic Note

After completing a task:

```markdown
---
id: ep-001
title: 20251030-first-analysis
type: episodic
date: 2025-10-30
task: first-analysis
tags: [task-completion]
outputs: [output/report.md]
related: [sem-001]
---

# First Analysis

## Goal
Understand concept X

## Approach
Analyzed materials A, B, C

## Findings
- Discovered pattern P
- Created sem-001 for concept X

## Outputs
- output/report.md

## Next Steps
- Analyze concept Y
```

---

### Maintenance Workflows

#### Daily (per task):
- Create/update notes as knowledge discovered
- Update indexes immediately after note creation

#### Weekly (every 3-5 tasks):
- Review episodic notes from recent tasks
- Extract common patterns to semantic notes
- Check for duplicate notes (merge if needed)
- Normalize tags across notes

#### Monthly:
- Audit all notes for staleness
- Review and consolidate procedural notes
- Check for broken cross-references
- Update outdated semantic notes

---

## Universal Application Examples

### Example 1: Research Paper Analysis

**Scenario**: Analyzing 50 research papers on machine learning.

**Without Memory**:
- Read all 50 papers each time (5000+ pages)
- Re-extract key concepts repeatedly
- Lose track of relationships between papers
- Context overflow

**With Memory**:

**Semantic Notes**:
- `sem-001`: Transformer architecture (stable concept)
- `sem-002`: Attention mechanism (foundational)
- `sem-003`: BERT vs GPT comparison (extracted insight)

**Episodic Notes**:
- `ep-001`: 20251020-papers-1-10 (first batch analysis)
- `ep-002`: 20251025-papers-11-25 (second batch)

**Task-Specific Index** (for writing review paper):
```json
{
  "task_id": "literature-review",
  "knowledge_requirements": {
    "critical": {
      "Transformer Basics": {
        "note_id": "sem-001",
        "sections_needed": ["entire note"],
        "estimated_lines": "300 lines"
      },
      "Recent Advances": {
        "note_id": "ep-002",
        "sections_needed": ["Findings section"],
        "estimated_lines": "200 lines"
      }
    }
  }
}
```

**Result**: 80% context reduction, consistent understanding

---

### Example 2: Legal Document Review

**Scenario**: Review 100 legal contracts for compliance.

**Without Memory**:
- Read all contracts for each question
- Re-identify patterns repeatedly
- Inconsistent interpretation
- Massive context usage

**With Memory**:

**Semantic Notes**:
- `sem-001`: Standard liability clause patterns
- `sem-002`: Termination conditions (common types)
- `sem-003`: Payment term variations

**Episodic Notes**:
- `ep-001`: 20251101-contracts-batch-1 (contracts 1-20)
- `ep-002`: 20251105-contracts-batch-2 (contracts 21-40)

**Procedural Notes**:
- `proc-001`: Contract review workflow
- `proc-002`: Compliance checklist

**Incremental Approach**:
```
Step 1: Review contracts 1-10 → Checkpoint (create sem-001)
Step 2: Review contracts 11-20 → Checkpoint (update sem-001)
Step 3: Review contracts 21-30 → Checkpoint (create sem-002)
...
```

**Result**: Consistent interpretation, 85% faster reviews

---

### Example 3: Software Documentation Synthesis

**Scenario**: Create unified documentation from 20 different API docs.

**Without Memory**:
- Read all 20 docs for each section
- Re-analyze API patterns each time
- Inconsistent terminology
- Context overflow

**With Memory**:

**Semantic Notes**:
- `sem-001`: REST API conventions (stable)
- `sem-002`: Authentication patterns (OAuth, JWT)
- `sem-003`: Error handling standards

**Episodic Notes**:
- `ep-001`: 20251110-api-analysis-phase-1
- `ep-002`: 20251112-api-analysis-phase-2

**Task-Specific Index** (for creating unified docs):
```json
{
  "task_id": "unified-api-docs",
  "knowledge_requirements": {
    "critical": {
      "Authentication": {
        "note_id": "sem-002",
        "sections_needed": ["entire note"],
        "estimated_lines": "400 lines"
      },
      "Common Patterns": {
        "source": "docs/api-1.md",
        "sections_needed": ["lines 50-100"],
        "estimated_lines": "50 lines"
      }
    }
  }
}
```

**Incremental Approach**:
```
Step 1: Analyze APIs 1-5 → Checkpoint (auth patterns)
Step 2: Write unified auth docs → Checkpoint
Step 3: Analyze APIs 6-10 → Checkpoint (error patterns)
Step 4: Write unified error docs → Checkpoint
...
```

**Result**: 90% context reduction, consistent terminology

---

### Example 4: Medical Literature Review

**Scenario**: Analyze 100 medical studies on treatment effectiveness.

**Without Memory**:
- Read all studies repeatedly
- Re-extract statistical methods each time
- Lose track of conflicting findings
- Context overflow

**With Memory**:

**Semantic Notes**:
- `sem-001`: RCT methodology (stable concept)
- `sem-002`: Meta-analysis techniques
- `sem-003`: Statistical significance thresholds
- `sem-004`: Treatment A efficacy (conflicting findings documented)

**Episodic Notes**:
- `ep-001`: 20251115-studies-cardiovascular (studies 1-25)
- `ep-002`: 20251118-studies-diabetes (studies 26-50)

**Procedural Notes**:
- `proc-001`: Medical study analysis workflow
- `proc-002`: Quality assessment checklist

**Incremental Approach**:
```
Step 1: Analyze cardiovascular studies 1-10 → Checkpoint
Step 2: Extract efficacy data → Checkpoint (create sem-004)
Step 3: Analyze cardiovascular studies 11-25 → Checkpoint
Step 4: Update efficacy analysis → Checkpoint (update sem-004)
...
```

**Result**: Systematic review, tracked conflicts, 88% context reduction

---

## Design Principles

### 1. Externalize, Don't Memorize
Store knowledge in files, not in conversation context.

### 2. Index First
Build indexes for fast retrieval, avoid full-text search.

### 3. Task-Driven Optimization
Create task-specific indexes for large materials.

### 4. Evidence-Based
Every claim links to source material.

### 5. Incremental Growth
Add knowledge as discovered, don't wait for completion.

### 6. Consistency Over Duplication
Update existing notes rather than creating duplicates.

### 7. Memory as Source of Truth
Use memory for consistency, not re-deriving facts.

### 8. Checkpoint Frequently
For large tasks, update memory every 15-30 minutes.

---

## Troubleshooting

### Issue 1: Note Not Found in Index

**Symptom**: Created note but can't find it.

**Cause**: Forgot to update indexes.

**Fix**:
1. Open note, check tags and topics
2. Update tags.json: add note ID to each tag
3. Update topics.json: add note ID to relevant topics

---

### Issue 2: Duplicate Notes

**Symptom**: Two notes cover the same concept.

**Cause**: Didn't check index before creating.

**Fix**:
1. Read both notes, determine which is comprehensive
2. Merge content into one note
3. Delete duplicate
4. Update indexes: remove duplicate ID
5. Update cross-references in other notes

---

### Issue 3: Task Index Didn't Save Context

**Symptom**: Task index still required reading 3000+ lines.

**Cause**: Marked too many sections as "critical".

**Fix**:
1. Review task deliverables
2. Narrow sections to specific line ranges
3. Move non-essential to "optional"
4. Re-estimate context usage

---

### Issue 4: Stale Note with Outdated Info

**Symptom**: Note describes outdated information.

**Cause**: Source material changed, note not updated.

**Fix**:
1. Re-analyze current material
2. Update note with new information
3. Increment "updated" date
4. Add note: "Updated YYYY-MM-DD: <what changed>"

---

### Issue 5: Can't Resume from Checkpoint

**Symptom**: Episodic note doesn't have enough context to resume.

**Cause**: Missing "Next Steps" or "Context for Next Step".

**Fix**:
1. Add "Next Steps" section with pending tasks
2. Add "Context for Next Step" with:
   - Memory notes needed
   - Previous outputs status
   - Focus area for next step
3. Test: "Can I resume from this?"

---

## Adaptation Guide

### For Different Domains

**Research**:
- Semantic notes: Concepts, theories, methodologies
- Episodic notes: Paper batches analyzed
- Procedural notes: Literature review workflow

**Legal**:
- Semantic notes: Legal patterns, precedents
- Episodic notes: Case/contract batches reviewed
- Procedural notes: Compliance checklist

**Medical**:
- Semantic notes: Treatments, methodologies, findings
- Episodic notes: Study batches analyzed
- Procedural notes: Quality assessment workflow

**Software**:
- Semantic notes: Architecture, patterns, APIs
- Episodic notes: Codebase analysis sessions
- Procedural notes: Code review checklist

### For Different Team Sizes

**Solo**:
- Personal memory system
- Informal note structure
- Focus on efficiency

**Small Team** (2-5):
- Shared memory repository
- Standard note templates
- Regular sync meetings

**Large Team** (>5):
- Centralized memory system
- Strict note standards
- Automated index validation
- Memory curator role

---

## Conclusion

The Memory-Driven Context Optimization System transforms how AI agents work with large materials by:

✅ **Reducing context usage by 80-95%** through persistent external memory
✅ **Maintaining consistency** by using memory as single source of truth
✅ **Enabling continuity** across sessions through episodic records
✅ **Scaling to large tasks** through incremental work with checkpoints
✅ **Building institutional knowledge** that grows over time

**Universal Applicability**: This system works for any scenario involving:
- Large material sets (research papers, legal docs, codebases)
- Long-term projects (multiple sessions)
- Knowledge synthesis (creating summaries, reviews, documentation)
- Iterative analysis (building understanding incrementally)

**Core Innovation**: Treating the file system as persistent external memory, optimized for AI agent retrieval through structured indexing and checkpoint-based incremental work.

---

## References

- Original implementation: CPG project (code analysis scenario)
- Inspired by: Human memory systems (semantic, episodic, procedural)
- Optimization techniques: Database indexing, compiler IR, OS paging

---

## License and Attribution

This design document is extracted from the CPG project's memory system (read branch) and generalized for universal application.

Feel free to adapt, extend, and apply to your own scenarios. Attribution appreciated but not required.

---

**Document Version**: 2.0.0
**Extracted**: 2025-10-30
**Status**: Production-ready design
**Applicability**: Universal (research, legal, medical, software, etc.)

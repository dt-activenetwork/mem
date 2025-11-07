# Procedural Memory

**Purpose**: Codify reusable workflows and checklists.

**What goes here**:
- Standard operating procedures (SOPs)
- Analysis workflows
- Documentation creation workflows
- Maintenance procedures
- Troubleshooting playbooks
- **Context overflow prevention procedures** ⚠️

## Core Procedures (Always Available)

- **`context-overflow-checkpointing.md`**: How to checkpoint work when context >70%, enable seamless resume after `/clear`
- **`memory-first-workflow.md`**: Memory-driven work approach
- **`memory-system-operations.md`**: How to use the memory system

**File naming**: `<domain>-<verb>-<slug>.md`
Example: `memory-system-operations.md`, `frontend-analysis-workflow.md`

**Note ID format**: `proc-NNN` (e.g., `proc-001`, `proc-002`)

**When to create**:
- Completed a workflow more than twice
- Identified a repeating pattern of steps
- User requests a formal procedure

**Structure**: See `/mo/memory/DESIGN.md` for complete note template.

**Required sections**:
- Purpose: Why this workflow exists
- Prerequisites: What's needed before starting
- Steps: Ordered, actionable steps
- Checks/Validation: How to verify correct execution
- Common Pitfalls: What to avoid and why
- Examples: Links to episodic notes showing workflow in action

**Key principle**: Refine iteratively based on experience.

# Temporary Working Directory

**Purpose**: Isolated workspace for intermediate artifacts during task execution.

**Lifecycle**:
1. **Created**: Automatically when task starts (if not exists)
2. **Used**: Store brainstorms, drafts, incomplete analysis, references
3. **Cleaned**: After task completes, move valuable artifacts to memory or delete

**What goes here**:
- ✅ Brainstorming notes (e.g., `TASK4_DEFECTS_BRAINSTORM.md`)
- ✅ Incomplete drafts (e.g., partial documentation)
- ✅ Status tracking files (e.g., `TASK4_COMPLETION_STATUS.md`)
- ✅ Reference materials from previous attempts
- ✅ Temporary analysis artifacts

**What does NOT go here**:
- ❌ Final deliverables (goes to `/result/`)
- ❌ Semantic knowledge (goes to `/memory/semantic/`)
- ❌ Task history (goes to `/memory/episodic/`)
- ❌ Persistent documentation

**Recommended subdirectory structure**:
```
/temp/task-<N>/
├── brainstorm/     # Initial ideas, defect lists, concept sketches
├── drafts/         # Incomplete documentation, partial analysis
├── analysis/       # Intermediate analysis results, data extractions
├── reference/      # Previous attempts, reference materials
└── STATUS.md       # (Optional) Real-time task progress tracking
```

**Cleanup policy**:
- **After task completion**: Review temp directory
- **Valuable insights**: Extract to semantic notes
- **Task progress**: Document in episodic note
- **Rest**: Delete or keep as reference (in `reference/` subdirectory)

**Git ignore**: All `/temp/` directories should be ignored by git.

# Context Overflow Prevention - Checkpointing Guide

**Purpose**: Enable seamless work continuation across `/clear` commands by saving state to memory.

**When to Use**: When context usage >70% or working on tasks that won't fit in remaining context.

---

## Quick Reference

```
Context >70% → Prepare for checkpoint
Context >80% → Checkpoint NOW
Multi-step task → Checkpoint between phases
```

---

## Checkpoint Creation Steps

### 1. Detect Need for Checkpoint

Check context usage BEFORE starting work:
```
If context_usage > 80%:
    Checkpoint immediately
Else if context_usage > 70% AND task_is_complex:
    Checkpoint before starting
Else if task_requires_multiple_phases:
    Checkpoint between phases
```

### 2. Create Checkpoint Episodic Note

File: `/memory/episodic/ep-XXX-[task-name]-checkpoint-YYYY-MM-DD.md`

Template:
```markdown
# Episodic Note: [Task Name] - Checkpoint

**Date**: YYYY-MM-DD HH:MM
**Status**: Checkpoint (Context: XX%)
**Phase**: [Current phase]
**Reason**: Context capacity approaching limit / Multi-phase task

---

## Current State

### Completed
- [x] Task/subtask 1
- [x] Task/subtask 2
- ...

### In Progress
- [ ] Current task: [Exact description]
  - Sub-step done: [what was completed]
  - Next sub-step: [what to do next]

### Not Started
- [ ] Future task 1
- [ ] Future task 2
- ...

---

## Files Modified

### Created
- `path/to/file1.ts`: [Purpose and key content]
- `path/to/file2.md`: [Purpose and key content]

### Modified
- `path/to/existing.ts`:
  - Changed: [What was changed]
  - Reason: [Why]
  - Line numbers: [Approximate location]

### To Be Modified (Next)
- `path/to/next-file.ts`: [Planned changes]

---

## Decisions Made

1. **Decision**: [What was decided]
   - **Rationale**: [Why this approach]
   - **Alternatives Considered**: [What else was considered]
   - **Impact**: [What this affects]

2. **Decision**: ...

---

## Knowledge Gained

### Discoveries
- [New insight about codebase/architecture/etc]
- [Pattern or approach that works well]

### Semantic Notes Created
- `sem-XXX-[topic].md`: [Brief description]

### Cross-References
- Related to: `sem-YYY`, `ep-ZZZ`
- See also: [Topic or area]

---

## Resume Plan

**Context**: When user runs `/clear`, AI will resume from this point.

### Immediate Next Steps (After /clear)

1. **Read this checkpoint note** (`ep-XXX-[task-name]-checkpoint.md`)
2. **Verify files exist**:
   - Check `file1.ts` was created
   - Check `file2.md` was modified
3. **Resume from**: [Exact step to start with]

### Detailed Resume Actions

1. [First action with specifics]
   - Files to read: [list]
   - Expected state: [what should exist]
   - Action: [what to do]

2. [Second action with specifics]
   - ...

3. Continue until task complete

### Success Criteria

Task is complete when:
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] All tests pass
- [ ] Documentation updated

---

## Context Data

### Intermediate Results
```
[Any data/results/calculations that need to be preserved]
```

### State Variables
- `variable_name`: value
- `current_index`: 5
- `processing_queue`: [item1, item2, ...]

### Environment Info
- Working directory: `/path/to/dir`
- Branch: `main`
- Last command: `npm test`

---

## Tags

`checkpoint`, `[task-category]`, `[specific-topic]`, `context-overflow-prevention`

## Related Notes

- Previous session: `ep-XXX`
- Semantic notes: `sem-YYY`, `sem-ZZZ`
- Next checkpoint: `ep-XXX+1` (if needed)
```

### 3. Update Indexes

**IMMEDIATELY after creating checkpoint**:

```bash
# Update tags.json
Add checkpoint note ID to:
- "checkpoint"
- "[task-category]"
- "[specific-topic]"

# Update topics.json
Add checkpoint note to relevant topic
```

### 4. Inform User

Say exactly:
```
⚠️  Context capacity at XX%. To prevent overflow, I've saved current progress to memory.

Checkpoint created: ep-XXX-[task-name]-checkpoint-YYYY-MM-DD.md

Please run: /clear

After clearing, I will:
1. Read the checkpoint from memory
2. Resume from exactly where we left off
3. Continue the work without asking you to repeat anything

The checkpoint includes:
- All completed work
- Current state and next steps
- Files modified and decisions made
- Complete resume plan
```

---

## Resuming After /clear

### Step 1: Locate Checkpoint

```bash
# Read tags.json to find latest checkpoint
grep "checkpoint" memory/index/tags.json

# Read the checkpoint episodic note
Read: memory/episodic/ep-XXX-[task-name]-checkpoint-YYYY-MM-DD.md
```

### Step 2: Restore Context

Read from checkpoint:
- Current State section → Know what's done
- Files Modified section → Know what exists
- Decisions Made section → Understand reasoning
- Resume Plan section → Know what to do next

### Step 3: Verify State

```bash
# Check files mentioned in checkpoint exist
Check: file1.ts exists and has expected content
Check: file2.md was modified correctly
```

### Step 4: Resume Work

Follow the "Resume Plan" section EXACTLY:
1. Start with first action in resume plan
2. Execute step by step
3. Update checkpoint note: "Resumed at HH:MM after /clear"

### Step 5: Complete Work

Continue until task is done, then:
1. Mark checkpoint as "Completed"
2. Create final episodic note for session
3. Update all semantic notes
4. Update indexes

---

## Examples

### Example 1: Simple Task Checkpoint

```markdown
# Episodic Note: Add User Authentication - Checkpoint

**Date**: 2025-11-06 15:30
**Status**: Checkpoint (Context: 85%)
**Phase**: Implementation - 60% complete

## Current State

### Completed
- [x] Created User model
- [x] Set up authentication routes
- [x] Implemented login endpoint

### In Progress
- [ ] Implementing password reset
  - Email template created
  - Token generation done
  - Next: Add reset endpoint handler

### Not Started
- [ ] Add session management
- [ ] Write tests

## Files Modified

### Created
- `src/models/User.ts`: User model with bcrypt password hashing
- `src/routes/auth.ts`: Login, register, logout routes
- `templates/reset-email.html`: Password reset email template

### To Be Modified
- `src/routes/auth.ts`: Add password reset endpoint

## Resume Plan

1. Add password reset POST endpoint to `src/routes/auth.ts`
2. Test reset flow manually
3. Write unit tests for auth routes
4. Update API documentation
```

### Example 2: Analysis Task Checkpoint

```markdown
# Episodic Note: Codebase Architecture Analysis - Checkpoint

**Date**: 2025-11-06 10:15
**Status**: Checkpoint (Context: 78%)
**Phase**: Analysis - Phase 2 of 4

## Current State

### Completed
- [x] Analyzed entry point (main.ts)
- [x] Mapped dependency graph
- [x] Identified 5 main modules

### In Progress
- [ ] Analyzing module: DataService
  - Read DataService.ts
  - Found 3 key patterns
  - Next: Analyze DatabaseConnector.ts

### Not Started
- [ ] Analyze remaining 3 modules
- [ ] Create architecture diagram
- [ ] Write summary report

## Knowledge Gained

### Discoveries
- Uses repository pattern for data access
- Singleton DatabaseConnector shared across services
- Event-driven architecture for async operations

### Semantic Notes Created
- `sem-042-repository-pattern.md`: Repository pattern implementation
- `sem-043-database-singleton.md`: Database connection management

## Resume Plan

1. Read `src/services/DatabaseConnector.ts`
2. Create semantic note for database patterns
3. Continue with remaining modules: AuthService, CacheService, QueueService
4. After all modules analyzed, create architecture diagram
5. Write final summary in `result/architecture-analysis.md`

## Context Data

### Module List (To Process)
- [x] DataService
- [ ] AuthService
- [ ] CacheService
- [ ] QueueService
- [ ] NotificationService

### Dependency Graph (So Far)
```
main.ts
  ├─ DataService (uses DatabaseConnector)
  ├─ AuthService (to analyze)
  └─ ...
```
```

---

## Best Practices

### DO ✅

- ✅ Checkpoint early (at 70-80% context)
- ✅ Include EXACT next steps
- ✅ List all modified files
- ✅ Save intermediate results
- ✅ Update indexes immediately
- ✅ Test checkpoint by imagining resume with zero context

### DON'T ❌

- ❌ Wait until context is 95% full
- ❌ Create vague checkpoint ("finish the task")
- ❌ Forget to update indexes
- ❌ Lose intermediate data
- ❌ Make user repeat instructions after /clear
- ❌ Skip verification step when resuming

---

## Troubleshooting

### Problem: Can't resume after /clear

**Cause**: Checkpoint lacks detail
**Fix**: Include exact file paths, line numbers, specific next action

### Problem: Lost intermediate data

**Cause**: Didn't save state variables/results
**Fix**: Add "Context Data" section with all intermediate info

### Problem: Context still fills up after checkpoint

**Cause**: Task is too large, needs multiple checkpoints
**Fix**: Create checkpoint after each major phase

---

## Related Documentation

- Main prompt: `/CLAUDE.md` - Context overflow prevention protocol
- Episodic notes guide: `/memory/episodic/README.md`
- Memory operations: `/memory/procedural/memory-system-operations.md`

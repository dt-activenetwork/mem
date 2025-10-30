# Framework Deployment Checklist

Use this checklist when deploying the framework to a new project.

---

## Pre-Deployment

- [ ] Read `README.md` to understand the framework
- [ ] Read `docs/QUICKSTART.md` for setup steps
- [ ] Verify you have access to the target project codebase

---

## Deployment Steps

### 1. Copy Framework

- [ ] Copy `/mo` directory to project: `cp -r /path/to/mo /project/claude`
- [ ] Verify directory structure: `tree -L 2 /project/claude`
- [ ] All directories present (memory/, prompt/, temp/, result/, docs/)

### 2. Configuration

- [ ] Copy config template: `cp config.example.yaml config.yaml`
- [ ] Fill in `project.name`
- [ ] Fill in `project.type`
- [ ] Fill in `project.description`
- [ ] Set `language.user_language` (e.g., "Chinese", "English")
- [ ] Set `language.think_language` (usually "English")
- [ ] Set `paths.base_dir` (usually "claude")
- [ ] Set `paths.codebase` (absolute path to code)
- [ ] Add at least 2-3 `objectives`
- [ ] Add `assumptions` about the project
- [ ] Save config.yaml

### 3. Template Instantiation

**Option A: Automatic (if script exists)**
- [ ] Run: `python scripts/instantiate.py config.yaml`
- [ ] Verify `CLAUDE.md` created (no `{{VARIABLES}}` present)
- [ ] Verify `prompt/0.overview.md` created

**Option B: Manual**
- [ ] Copy: `cp CLAUDE.md.template CLAUDE.md`
- [ ] Copy: `cp prompt/0.overview.md.template prompt/0.overview.md`
- [ ] Replace all `{{PROJECT_NAME}}` with actual name
- [ ] Replace all `{{PROJECT_TYPE}}` with actual type
- [ ] Replace all `{{PROJECT_DESCRIPTION}}` with actual description
- [ ] Replace all `{{USER_LANGUAGE}}` with user language
- [ ] Replace all `{{THINK_LANGUAGE}}` with "English"
- [ ] Replace all `{{BASE_DIR}}` with "claude"
- [ ] Search for remaining `{{` in both files → should return 0 matches

### 4. Git Configuration

- [ ] Add to `.gitignore`: `claude/temp/`
- [ ] (Optional) Add to `.gitignore`: `claude/memory/semantic/`
- [ ] (Optional) Add to `.gitignore`: `claude/memory/episodic/`
- [ ] Commit `.gitignore` changes

### 5. Verification

- [ ] Check file count: `find claude -type f | wc -l` (should be ~17+)
- [ ] Check CLAUDE.md: `grep "{{" claude/CLAUDE.md` (should return no matches)
- [ ] Check 0.overview.md: `grep "{{" claude/prompt/0.overview.md` (should return no matches)
- [ ] Check config.yaml exists and is filled
- [ ] Check all READMEs present:
  - [ ] `claude/README.md`
  - [ ] `claude/docs/QUICKSTART.md`
  - [ ] `claude/memory/semantic/README.md`
  - [ ] `claude/memory/episodic/README.md`
  - [ ] `claude/memory/procedural/README.md`
  - [ ] `claude/temp/README.md`
  - [ ] `claude/result/README.md`

---

## First Session

### 6. Initialize with Claude Code

- [ ] Start Claude Code in project directory
- [ ] Send: "Hello! Please read the CLAUDE.md file in the claude/ directory."
- [ ] Verify Claude reads:
  - [ ] CLAUDE.md
  - [ ] prompt/0.overview.md
  - [ ] prompt/0.memory.md
  - [ ] memory/index/tags.json
  - [ ] memory/index/topics.json
- [ ] Verify Claude confirms memory system is ready

### 7. Test Memory System

- [ ] Give Claude a simple analysis task (e.g., "Analyze the main.py file")
- [ ] Verify Claude:
  - [ ] Checks memory first (queries indexes)
  - [ ] Analyzes the code
  - [ ] Creates semantic note (e.g., `sem-001`)
  - [ ] Creates episodic note (e.g., `ep-001`)
  - [ ] Updates `tags.json` and `topics.json`
  - [ ] Creates output in `result/1/`
- [ ] Check files created:
  - [ ] `ls claude/memory/semantic/` (should show `*.md` file)
  - [ ] `ls claude/memory/episodic/` (should show `*.md` file)
  - [ ] `cat claude/memory/index/tags.json` (should have tags)
  - [ ] `ls claude/result/1/` (should have output file)

### 8. Test Memory Reuse

- [ ] Start new session (or clear context)
- [ ] Ask Claude about the previous analysis (e.g., "What did you find in main.py?")
- [ ] Verify Claude:
  - [ ] Queries memory indexes
  - [ ] Reads semantic note (sem-001)
  - [ ] Answers from memory (without re-reading code)
- [ ] If Claude answers correctly from memory → **Success!**

---

## Post-Deployment

### 9. Documentation

- [ ] Add project-specific task prompts to `prompt/`
- [ ] Document key objectives in `0.overview.md`
- [ ] (Optional) Create project-specific README in `claude/` root

### 10. Team Onboarding (if applicable)

- [ ] Share `claude/README.md` with team
- [ ] Share `claude/docs/QUICKSTART.md` for setup
- [ ] Explain git ignore policy (temp/ not committed)
- [ ] Explain that memory/ can optionally be kept private

---

## Troubleshooting

### Issue: Templates not instantiated

**Symptom**: See `{{VARIABLES}}` in CLAUDE.md or 0.overview.md

**Fix**: Go back to step 3 and complete template instantiation

### Issue: Claude not reading memory

**Symptom**: Claude re-analyzes code already in semantic notes

**Fix**:
- Remind Claude: "Please read memory indexes first"
- Check that `0.memory.md` is in place
- Verify semantic notes exist in `memory/semantic/`

### Issue: High context usage

**Symptom**: Context >5000 lines for simple tasks

**Fix**:
- For large tasks, build task-specific index first
- See `memory/procedural/memory-system-operations.md` → Operation 5
- Ensure Claude uses Memory-First workflow

---

## Success Criteria

✅ **Framework deployed successfully if:**

1. All files copied to target project
2. `config.yaml` filled with project details
3. Templates instantiated (no `{{VARIABLES}}` remaining)
4. Git ignore configured
5. Claude reads CLAUDE.md and confirms memory system ready
6. Claude creates semantic/episodic notes on first task
7. Claude reuses memory on second task (doesn't re-analyze)

If all 7 criteria met → **Deployment successful! Framework is operational.**

---

## Next Steps After Deployment

1. **Week 1**: Let Claude build up semantic knowledge (analyze key modules)
2. **Week 2**: Create task-specific indexes for large analysis tasks
3. **Month 1**: Run memory hygiene cycle (every 3-5 tasks)
4. **Ongoing**: Monitor context reduction metrics, aim for >70%

---

**Checklist Version**: 1.0.0
**Last Updated**: 2025-10-30

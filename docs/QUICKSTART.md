# Quick Start Guide - Claude Code Memory System

**Goal**: Get the memory system running in your project in < 15 minutes.

---

## Prerequisites

- [ ] You have a codebase you want to analyze/document
- [ ] You have Claude Code installed and working
- [ ] You understand basic concepts of AI agent prompting

---

## Step 1: Copy Framework to Your Project (2 minutes)

### Option A: Direct Copy

```bash
# Navigate to your project root
cd /path/to/your-project

# Copy the framework
cp -r /path/to/mo ./claude

# Verify structure
tree -L 2 claude/
```

### Option B: Git Submodule (if framework is in a git repo)

```bash
cd /path/to/your-project
git submodule add <framework-repo-url> claude
```

**Result**: You should now have a `claude/` directory in your project.

---

## Step 2: Configure for Your Project (5 minutes)

### 2.1 Create Configuration File

```bash
cd claude
cp config.example.yaml config.yaml
```

### 2.2 Edit Configuration

Open `config.yaml` and fill in:

```yaml
project:
  name: "MyAwesomeProject"          # ← Your project name
  type: "Python FastAPI backend"    # ← What kind of project?
  description: "A REST API for user management"  # ← Brief description

language:
  user_language: "English"          # ← Language for docs/outputs
  think_language: "English"         # ← Usually keep English

paths:
  base_dir: "claude"                # ← Directory name (usually "claude")
  codebase: "/home/user/my-project" # ← Path to your code

objectives:
  - "Analyze authentication module and document the design"
  - "Identify potential security vulnerabilities"
  - "Create API documentation"

assumptions:
  - "The project uses SQLAlchemy for ORM"
  - "JWT tokens are used for authentication"
```

**Tips**:
- `user_language`: Use "Chinese", "English", "Japanese", etc. based on your preference
- `think_language`: Keep "English" for best technical reasoning
- `objectives`: List main goals for the AI agent (you can add more later)
- `assumptions`: Document key facts about your project

---

## Step 3: Instantiate Templates (3 minutes)

### Option A: Automatic (if script exists)

```bash
# Run instantiation script
python scripts/instantiate.py config.yaml

# This generates:
# - CLAUDE.md (from CLAUDE.md.template)
# - prompt/0.overview.md (from 0.overview.md.template)
```

### Option B: Manual

If script doesn't exist, manually replace variables in templates:

1. **Copy templates**:
   ```bash
   cp CLAUDE.md.template CLAUDE.md
   cp prompt/0.overview.md.template prompt/0.overview.md
   ```

2. **Replace variables**:
   Open each file and replace:
   - `{{PROJECT_NAME}}` → "MyAwesomeProject"
   - `{{PROJECT_TYPE}}` → "Python FastAPI backend"
   - `{{PROJECT_DESCRIPTION}}` → "A REST API for user management"
   - `{{USER_LANGUAGE}}` → "English"
   - `{{THINK_LANGUAGE}}` → "English"
   - `{{BASE_DIR}}` → "claude"
   - etc.

**Tip**: Use find-and-replace in your text editor to speed this up.

---

## Step 4: Setup Git Ignore (1 minute)

Add to your project's `.gitignore`:

```gitignore
# Claude AI temporary working directory
claude/temp/

# Optional: Keep memory private (if sensitive)
# claude/memory/semantic/
# claude/memory/episodic/
```

**Why**:
- `/temp/` contains drafts and brainstorms (no need to commit)
- Memory can optionally be kept private if it contains sensitive analysis

---

## Step 5: Verify Setup (2 minutes)

Check that everything is in place:

```bash
# Check directory structure
tree -L 2 claude/

# Should see:
# claude/
# ├── CLAUDE.md                  ✅ Instantiated
# ├── config.yaml                ✅ Configured
# ├── memory/
# │   ├── DESIGN.md
# │   ├── semantic/
# │   ├── episodic/
# │   ├── procedural/
# │   ├── system/
# │   └── index/
# ├── prompt/
# │   ├── 0.overview.md          ✅ Instantiated
# │   └── 0.memory.md
# ├── temp/
# └── result/

# Check that templates were instantiated
grep "MyAwesomeProject" claude/CLAUDE.md
# Should return matches (not "{{PROJECT_NAME}}")
```

**If you see `{{VARIABLES}}` still present → Go back to Step 3 and complete instantiation**

---

## Step 6: First Session with Claude Code (5 minutes)

### 6.1 Start Claude Code

```bash
cd /path/to/your-project
claude code
```

### 6.2 First Interaction

```
You: Hello! Please read the CLAUDE.md file in the claude/ directory.

Claude: [Reads CLAUDE.md, 0.overview.md, 0.memory.md]
        Hello! I've initialized the memory system.
        - Read tags.json and topics.json (both empty - fresh start)
        - No existing semantic/episodic notes yet
        Ready to start working on MyAwesomeProject.

You: Please analyze the authentication module (auth.py) and create documentation.

Claude: [Follows Memory-First workflow]
        Step 1: Checking memory for existing knowledge about authentication...
                → tags.json query: "auth", "authentication" → No results
                → This is new analysis, proceeding to read code

        Step 2: Reading /path/to/code/auth.py and analyzing...
                [Analyzes code]

        Step 3: Creating semantic note (sem-001) documenting authentication design...
                Creating: /claude/memory/semantic/auth-jwt-pattern.md
                Updating: /claude/memory/index/tags.json
                Updating: /claude/memory/index/topics.json

        Step 4: Creating documentation...
                Writing: /claude/result/1/auth-documentation.md

        Step 5: Creating episodic note (ep-001) recording this session...
                Creating: /claude/memory/episodic/20251030-t1-auth-analysis.md

        Done! Created:
        - Semantic note: sem-001 (authentication JWT pattern)
        - Documentation: /claude/result/1/auth-documentation.md
        - Episodic note: ep-001 (session record)
```

### 6.3 Verify Memory System is Working

```bash
# Check that memory was created
cat claude/memory/index/tags.json
# Should now contain tags like "authentication", "jwt", "security"

ls claude/memory/semantic/
# Should show: auth-jwt-pattern.md

ls claude/memory/episodic/
# Should show: 20251030-t1-auth-analysis.md

ls claude/result/1/
# Should show: auth-documentation.md
```

**If files were created → Success! Memory system is working.**

---

## Step 7: Test Memory Reuse (2 minutes)

Start a new session to verify memory persistence:

```
You: In a previous session, you analyzed authentication.
     Can you tell me how JWT tokens are validated?

Claude: [Follows Memory-First workflow]
        Step 1: Checking memory for "JWT" and "authentication"...
                → Found sem-001 (auth-jwt-pattern.md)
                → Reading sem-001...

        Based on previous analysis (sem-001), JWT tokens are validated by:
        1. [Explanation from memory, no need to re-read code]
        2. [Evidence from sem-001 with file:line references]

        This knowledge was captured during Task 1 (ep-001).
```

**If Claude answered from memory without re-reading code → Success! Memory reuse is working.**

---

## Next Steps

### Immediate (First Week)

1. **Add more tasks**: Create task prompt files in `prompt/` for specific goals
2. **Build semantic knowledge**: Let Claude analyze key modules and create semantic notes
3. **Establish workflows**: Identify repeating patterns and create procedural notes

### Short-term (First Month)

1. **Create task-specific indexes**: For large analysis tasks (>5000 lines context)
2. **Memory hygiene**: Every 3-5 tasks, run a hygiene cycle to merge duplicates
3. **Extend system knowledge**: Add universal tool knowledge to `memory/system/tools/`

### Long-term

1. **Cross-project reuse**: Copy `memory/system/` to other projects
2. **Customize workflows**: Refine procedural notes based on experience
3. **Track metrics**: Measure context reduction and memory effectiveness

---

## Common Issues

### Issue: "AI is not reading memory at session start"

**Fix**:
- Explicitly remind AI: "Please read the memory indexes first"
- Check that `0.memory.md` contains Memory-First workflow instructions

### Issue: "Context usage is still high"

**Fix**:
- For large tasks, build task-specific index BEFORE starting
- See `memory/procedural/memory-system-operations.md` → Operation 5

### Issue: "AI created notes but didn't update indexes"

**Fix**:
- Remind AI: "Please update tags.json and topics.json"
- This should be automatic; if not, file a bug report

See full troubleshooting guide in `README.md`.

---

## Getting Help

- **Documentation**: Read `memory/DESIGN.md` for complete architecture
- **Workflows**: Read `memory/procedural/memory-system-operations.md` for detailed procedures
- **Examples**: Check the CPG project (where this framework originated) for real-world usage

---

**Estimated Total Time**: 15-20 minutes for complete setup

**You're now ready to use the Claude Code Memory System!**

Next: Create your first task prompt and let Claude start building knowledge about your codebase.

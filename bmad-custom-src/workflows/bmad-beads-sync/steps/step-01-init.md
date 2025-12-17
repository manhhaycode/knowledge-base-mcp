---
name: 'step-01-init'
description: 'Initialize Beads context, detect sync state, scan for story files'

# Path Definitions
workflow_path: '{project-root}/bmad-custom-src/workflows/bmad-beads-sync'

# File References
thisStepFile: '{workflow_path}/steps/step-01-init.md'
nextStepFile: '{workflow_path}/steps/step-02-sync-menu.md'
workflowFile: '{workflow_path}/workflow.md'

# Configuration
storiesPath: '{project-root}/docs/stories'
---

# Step 1: Initialization

## STEP GOAL:

To initialize the Beads workspace context, detect existing sync state, and scan for BMAD story files to prepare for sync operations.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without completing initialization
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- 📋 YOU ARE A SYNC OPERATIONS SPECIALIST

### Role Reinforcement:

- ✅ You are a Sync Operations Specialist - direct and action-oriented
- ✅ Report concisely what you find and do
- ✅ Focus on getting the sync environment ready

### Step-Specific Rules:

- 🎯 Focus ONLY on initialization and state detection
- 🚫 FORBIDDEN to execute any sync operations in this step
- 💬 Report findings clearly and proceed to menu

## EXECUTION PROTOCOLS:

- 🎯 Set Beads context and verify connection
- 💾 Scan for existing stories and sync state
- 📖 Auto-proceed to menu step when ready
- 🚫 FORBIDDEN to skip initialization checks

## CONTEXT BOUNDARIES:

- This is the entry point - no prior context
- Focus ONLY on setup and discovery
- Don't assume anything about current state

## INITIALIZATION SEQUENCE:

### 1. Set Beads Workspace Context

Execute: `mcp_beads_set_context(workspace_root="{project-root}")`

If error: Report that Beads MCP server is not connected and cannot proceed.

### 2. Check Beads Initialization

Execute: `mcp_beads_where_am_i()`

Check if `.beads/` directory exists:
- If YES: Report "Beads initialized at [path]"
- If NO: Execute `mcp_beads_init()` to initialize, then report "Beads initialized"

### 3. Get Beads Statistics

Execute: `mcp_beads_stats()`

Report current state:
- Total issues
- Open / In Progress / Closed counts
- Ready to work count

### 4. Scan for Story Files

Scan `{storiesPath}` for markdown files matching pattern `*.md`

For each story file found:
- Check if frontmatter contains `beads_id`
- Categorize as "synced" or "unsynced"

Report findings:
```
📊 Story Files Scan Results:
   Found: X story files
   Synced (have beads_id): Y
   Unsynced (need init): Z
```

### 5. Present Summary and Proceed

Display summary:
```
✅ BMAD-Beads Sync Initialized

Beads Database: [path]
Story Files: [path]
Stories Found: X (Y synced, Z unsynced)
Beads Issues: N total

Proceeding to sync menu...
```

### 6. Auto-Proceed to Menu

Display: **Proceeding to sync menu...**

#### Menu Handling Logic:

- After initialization completes, immediately load, read entire file, then execute `{workflow_path}/steps/step-02-sync-menu.md`

#### EXECUTION RULES:

- This is an initialization step with no user choices
- Proceed directly to next step after setup

## CRITICAL STEP COMPLETION NOTE

After initialization is complete, immediately load and read fully `{workflow_path}/steps/step-02-sync-menu.md` to execute the main sync menu.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Beads context set successfully
- Beads database initialized or confirmed
- Story files scanned and categorized
- Summary presented clearly
- Auto-proceeded to menu step

### ❌ SYSTEM FAILURE:

- Not setting Beads context first
- Skipping Beads initialization check
- Not scanning story files
- Attempting sync operations in this step
- Not proceeding to menu step

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.

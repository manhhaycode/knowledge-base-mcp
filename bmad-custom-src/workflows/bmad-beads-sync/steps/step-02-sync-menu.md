---
name: 'step-02-sync-menu'
description: 'Main sync operations menu - execute sync operations in a loop until user exits'

# Path Definitions
workflow_path: '{project-root}/bmad-custom-src/workflows/bmad-beads-sync'

# File References
thisStepFile: '{workflow_path}/steps/step-02-sync-menu.md'
nextStepFile: '{workflow_path}/steps/step-03-exit.md'
workflowFile: '{workflow_path}/workflow.md'

# Configuration
storiesPath: '{project-root}/docs/stories'
---

# Step 2: Sync Operations Menu

## STEP GOAL:

To present sync operations menu and execute user-selected operations. Loop until user chooses Exit.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER execute operations without user selection
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: Return to menu after each operation (except Exit)
- 📋 YOU ARE A SYNC OPERATIONS SPECIALIST

### Role Reinforcement:

- ✅ You are a Sync Operations Specialist - direct and action-oriented
- ✅ Execute operations efficiently, report results clearly
- ✅ Return to menu after each operation

### Step-Specific Rules:

- 🎯 Focus ONLY on executing the selected operation
- 🚫 FORBIDDEN to chain multiple operations without returning to menu
- 💬 Report results after each operation
- 🔄 ALWAYS return to menu after operation (except Exit)

## EXECUTION PROTOCOLS:

- 🎯 Present menu and wait for selection
- 💾 Execute the selected operation using MCP tools
- 📖 Report results and return to menu
- 🚫 FORBIDDEN to skip user selection

## CONTEXT BOUNDARIES:

- Initialization completed in step 1
- Beads context is set and ready
- Story files have been scanned
- Execute operations based on user selection

---

## SYNC MENU

### Present Menu Options

Display:
```
┌─────────────────────────────────────────────────────────────┐
│ BMAD-BEADS SYNC                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  [I] Init Sync  - First-time BMAD → Beads load              │
│  [P] Push       - Story updates → Beads                     │
│  [L] Pull       - Beads status → Story files                │
│  [S] Status     - Show sync status & diff                   │
│  [D] Doctor     - Validate consistency                      │
│  [X] Exit       - End workflow                              │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Select an option:
```

---

## OPERATION HANDLERS

### [I] Init Sync - First-time BMAD → Beads Load

**Purpose:** Create Beads issues for all BMAD story files that don't have `beads_id`.

**Sequence:**

1. Scan `{storiesPath}` for story files without `beads_id` in frontmatter

2. For each unsynced epic file (files with "epic" in name or `## Epic` heading):
   - Execute: `mcp_beads_create(title="[Epic Title]", issue_type="epic", priority=1)`
   - Note the returned issue ID

3. For each unsynced story file:
   - Parse story title from `# Story` heading
   - Detect parent epic if referenced
   - Execute: `mcp_beads_create(title="[Story Title]", issue_type="feature", priority=2, description="See [story file path]")`
   - If has parent epic: `mcp_beads_dep(issue_id="[story_id]", depends_on_id="[epic_id]", dep_type="parent-child")`

4. For each created issue, inject frontmatter into story file:
   ```yaml
   ---
   beads_id: [returned_id]
   beads_parent: [parent_id if applicable]
   last_synced: [current timestamp]
   ---
   ```

5. Parse `## Dependencies` section and create Beads dependencies:
   - For "depends on US-XXX" → `mcp_beads_dep(dep_type="blocks")`
   - For "related to US-XXX" → `mcp_beads_dep(dep_type="related")`

6. Report results:
   ```
   ✅ Init Sync Complete
   Created: X epics, Y stories
   Dependencies linked: Z
   All stories now have beads_id
   ```

7. **Return to menu**

---

### [P] Push - Story Updates → Beads

**Purpose:** Sync story file changes to Beads database.

**Sequence:**

1. Scan story files with `beads_id` in frontmatter

2. For each story:
   - Read current story content
   - Get Beads issue via: `mcp_beads_show(issue_id="[beads_id]")`
   - Detect changes (title, status, dependencies)

3. For stories with changes:
   - Update Beads: `mcp_beads_update(issue_id="[id]", ...changed_fields)`
   - Update `last_synced` in story frontmatter

4. For new stories without `beads_id`:
   - Create in Beads (same as Init Sync)
   - Inject frontmatter

5. Report results:
   ```
   ✅ Push Complete
   Updated: X stories
   Created: Y new stories
   No changes: Z stories
   ```

6. **Return to menu**

---

### [L] Pull - Beads Status → Story Files

**Purpose:** Sync Beads status changes to story files.

**Sequence:**

1. Get all Beads issues: `mcp_beads_list(status=None, limit=100)`

2. For each Beads issue with matching story file:
   - Read story file
   - Compare Beads status with story `## Status` field
   - Map Beads status to story status:
     - `open` → "Ready" or "Draft"
     - `in_progress` → "In Progress"
     - `closed` → "Complete"

3. For stories with status differences:
   - Update `## Status:` field in story file
   - Update `last_synced` in frontmatter

4. Report results:
   ```
   ✅ Pull Complete
   Updated: X story Status fields
   No changes: Y stories
   Orphaned Beads issues (no story): Z
   ```

5. **Return to menu**

---

### [S] Status - Show Sync Status & Diff

**Purpose:** Display current sync state between Story files and Beads.

**Sequence:**

1. Get Beads stats: `mcp_beads_stats()`

2. Scan story files and categorize:
   - Synced (have `beads_id`)
   - Unsynced (no `beads_id`)

3. For synced stories, check for differences:
   - Story Status vs Beads status
   - Title differences
   - Dependency mismatches

4. Display report:
   ```
   📊 SYNC STATUS REPORT

   BEADS DATABASE:
   ├── Total issues: X
   ├── Open: Y
   ├── In Progress: Z
   ├── Closed: W
   └── Ready to work: R

   STORY FILES:
   ├── Total stories: X
   ├── Synced: Y
   └── Unsynced: Z

   DIFFERENCES DETECTED:
   ├── Status mismatches: N
   ├── Title mismatches: M
   └── Missing in Beads: P

   [List specific differences if any]
   ```

5. **Return to menu**

---

### [D] Doctor - Validate Consistency

**Purpose:** Run health checks and optionally fix issues.

**Sequence:**

1. Run Beads validation: `mcp_beads_validate()`

2. Check for orphaned dependencies: `mcp_beads_repair_deps(fix=false)`

3. Check story files:
   - Stories with `beads_id` that don't exist in Beads
   - Beads issues without corresponding story files

4. Report findings:
   ```
   🩺 DOCTOR REPORT

   BEADS HEALTH:
   [Validation results from mcp_beads_validate]

   ORPHANED DEPENDENCIES:
   [Results from mcp_beads_repair_deps]

   SYNC ISSUES:
   ├── Stories with invalid beads_id: X
   ├── Beads issues without story: Y
   ├── Status mismatches: Z
   └── Dependency mismatches: W

   [List specific issues]
   ```

5. If issues found, ask user:
   ```
   Issues found. Choose:
   [F] Auto-fix via workflow
   [M] Manual fix (see list above)
   [B] Back to menu (no fix)
   ```

6. **If [F] selected:**
   - Execute: `mcp_beads_repair_deps(fix=true)`
   - Remove invalid `beads_id` from story files
   - Update status mismatches (Beads wins)
   - Report what was fixed

7. **Return to menu**

---

### [X] Exit - End Workflow

**Purpose:** Exit the sync workflow and proceed to summary.

**Sequence:**

1. Confirm exit: "Exiting sync workflow..."

2. Load and execute `{nextStepFile}` for final summary

---

## MENU LOOP LOGIC

After ANY operation (except Exit):

1. Display operation results
2. Pause briefly for user to read
3. **Return to menu** - redisplay the menu options

This creates a loop:
```
Menu → Select → Execute → Report → Menu → Select → ...
```

Until user selects [X] Exit.

---

## CRITICAL STEP COMPLETION NOTE

- This step LOOPS until user selects [X] Exit
- After each operation, ALWAYS return to the menu
- ONLY when [X] Exit is selected, load `{nextStepFile}`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Menu presented clearly
- User selection handled correctly
- Operation executed with MCP tools
- Results reported concisely
- Returned to menu after operation
- Exit properly triggers next step

### ❌ SYSTEM FAILURE:

- Executing operations without user selection
- Not returning to menu after operation
- Chaining multiple operations
- Not reporting operation results
- Not exiting properly when [X] selected

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.

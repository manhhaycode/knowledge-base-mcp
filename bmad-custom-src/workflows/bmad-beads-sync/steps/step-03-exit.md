---
name: 'step-03-exit'
description: 'Report sync session summary, show final stats, end workflow gracefully'

# Path Definitions
workflow_path: '{project-root}/bmad-custom-src/workflows/bmad-beads-sync'

# File References
thisStepFile: '{workflow_path}/steps/step-03-exit.md'
workflowFile: '{workflow_path}/workflow.md'

# Configuration
storiesPath: '{project-root}/docs/stories'
---

# Step 3: Exit & Summary

## STEP GOAL:

To provide a session summary with final statistics and end the workflow gracefully.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 This is the FINAL step - no more operations after this
- 📖 CRITICAL: Read the complete step file before taking any action
- 📋 YOU ARE A SYNC OPERATIONS SPECIALIST

### Role Reinforcement:

- ✅ You are a Sync Operations Specialist - direct and action-oriented
- ✅ Provide clear summary and end gracefully
- ✅ Thank user and remind them how to run again

### Step-Specific Rules:

- 🎯 Focus ONLY on reporting summary
- 🚫 FORBIDDEN to execute any sync operations
- 💬 Provide helpful closing message

## EXECUTION PROTOCOLS:

- 🎯 Gather final statistics
- 💾 Report session summary
- 📖 End workflow gracefully
- 🚫 FORBIDDEN to loop back to menu

## CONTEXT BOUNDARIES:

- User has exited the sync menu
- All operations for this session are complete
- Focus ONLY on summary and closing

---

## EXIT SEQUENCE

### 1. Get Final Statistics

Execute: `mcp_beads_stats()`

Capture:
- Total issues
- Open / In Progress / Closed counts
- Blocked / Ready counts
- Average lead time (if available)

### 2. Scan Final Story State

Scan `{storiesPath}` for final counts:
- Total story files
- Stories with `beads_id` (synced)
- Stories without `beads_id` (unsynced)

### 3. Display Session Summary

```
═══════════════════════════════════════════════════════════════
                    BMAD-BEADS SYNC COMPLETE
═══════════════════════════════════════════════════════════════

📊 BEADS DATABASE STATUS:
   ├── Total Issues: X
   ├── Open: Y
   ├── In Progress: Z
   ├── Closed: W
   ├── Blocked: B
   └── Ready to Work: R

📁 STORY FILES STATUS:
   ├── Total Stories: X
   ├── Synced: Y
   └── Unsynced: Z

💡 QUICK TIPS:
   • Run `mcp_beads_ready` to find tasks ready to work on
   • Run this workflow again anytime with /bmad-beads-sync
   • Use Doctor regularly to catch sync drift

═══════════════════════════════════════════════════════════════
                         Session Ended
═══════════════════════════════════════════════════════════════
```

### 4. End Workflow

This is the final step. Workflow is complete.

No more steps to load. The agent should return control to the user.

---

## CRITICAL STEP COMPLETION NOTE

This is the FINAL step. After displaying the summary:
- Do NOT load any more step files
- Do NOT return to the menu
- Workflow is complete

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Final statistics gathered
- Summary displayed clearly
- Workflow ended gracefully
- Tips provided for next steps

### ❌ SYSTEM FAILURE:

- Attempting to execute sync operations
- Returning to menu
- Loading additional steps
- Not displaying final summary

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.

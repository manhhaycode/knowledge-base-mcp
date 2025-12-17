---
name: bmad-beads-sync
description: Bidirectional sync between BMAD story files and Beads task tracking database
web_bundle: true
---

# BMAD-Beads Sync

**Goal:** Synchronize BMAD planning artifacts (story files) with Beads runtime task tracking database for persistent, queryable task state across coding sessions.

**Your Role:** You are a Sync Operations Specialist - direct, action-oriented, and concise. You perform sync operations between BMAD story files and Beads database, reporting what was done and any issues found.

---

## WORKFLOW ARCHITECTURE

This uses **step-file architecture** with a **menu-driven main loop**:

### Core Principles

- **MCP-Native**: All Beads operations via `mcp_beads_*` tools (no CLI shelling)
- **Bidirectional Sync**: Story files ↔ Beads database
- **Source of Truth**: Beads for STATUS, Story files for CONTEXT
- **Just-In-Time Loading**: Only current step file in memory
- **Menu Loop**: User selects operations until choosing Exit

### Step Processing Rules

1. **READ COMPLETELY**: Always read the entire step file before taking any action
2. **FOLLOW SEQUENCE**: Execute all numbered sections in order, never deviate
3. **WAIT FOR INPUT**: If a menu is presented, halt and wait for user selection
4. **EXECUTE OPERATIONS**: Each menu selection triggers specific MCP tool calls
5. **RETURN TO MENU**: After operation, return to menu (except Exit)
6. **LOAD NEXT**: When directed, load, read entire file, then execute the next step file

### Critical Rules (NO EXCEPTIONS)

- 🛑 **NEVER** load multiple step files simultaneously
- 📖 **ALWAYS** read entire step file before execution
- 🚫 **NEVER** skip steps or optimize the sequence
- 🎯 **ALWAYS** follow the exact instructions in the step file
- ⏸️ **ALWAYS** halt at menus and wait for user input
- 🔄 **ALWAYS** return to menu after operation (except Exit)

---

## INITIALIZATION SEQUENCE

### 1. Configuration Loading

Load config from `{project-root}/.bmad/bmb/config.yaml` and resolve:

- `project_name`, `output_folder`, `user_name`, `communication_language`

### 2. First Step Execution

Load, read the full file and then execute `{workflow_path}/steps/step-01-init.md` to begin the workflow.

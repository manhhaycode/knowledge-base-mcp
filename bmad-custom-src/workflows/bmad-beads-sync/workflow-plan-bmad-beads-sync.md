---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
workflowComplete: true
---

# Workflow Creation Plan: bmad-beads-sync

## Initial Project Context

- **Module:** stand-alone
- **Target Location:** /Users/manhhaycode/Developer/knowledge-base/bmad-custom-src/workflows/bmad-beads-sync
- **Created:** 2025-12-17

## Tools Configuration

### Core BMAD Tools
- **Party-Mode**: ❌ Excluded - Sync is action-focused, not creative
- **Advanced Elicitation**: ❌ Excluded - No deep questioning needed
- **Brainstorming**: ❌ Excluded - No ideation phase

### LLM Features
- **File I/O**: ✅ Included - Essential for reading/writing story files
- **Web-Browsing**: ❌ Excluded - No external data needed
- **Sub-Agents**: ❌ Excluded - Single workflow, no delegation
- **Sub-Processes**: ❌ Excluded - Sequential operations suffice

### Memory Systems
- **Sidecar File**: ❌ Excluded - Beads already tracks state

### External Integrations
- **Beads MCP**: ✅ Required - Primary sync via `mcp_beads_*` tools
- **Git Integration**: ❌ Excluded - Not needed
- **Other MCP**: ❌ None required

### Installation Requirements
- Beads MCP server must be connected
- No additional installations required

## Workflow Overview

**Purpose:** Create a bidirectional sync workflow between BMAD planning artifacts (story files) and Beads runtime task tracking database.

**Problem Solved:** BMAD excels at front-loaded planning with rich context but lacks runtime task tracking across sessions. Beads provides queryable work queues for agents but needs BMAD's structured context. This workflow bridges that gap.

## Reference Document

Source requirements: `/Users/manhhaycode/Developer/knowledge-base/compass_artifact_wf-e155a896-6865-4481-bc7a-58c1a96b4d54_text_markdown.md`

---

## Requirements (Gathered from Party Mode Discussion)

### 1. Workflow Purpose and Scope

| Aspect | Decision |
|--------|----------|
| **Problem** | BMAD story files disconnected from runtime task tracking |
| **Primary User** | Developers/agents using BMAD method needing persistent task state |
| **Main Outcome** | Synchronized issue tracking: Story files ↔ Beads database |

### 2. Workflow Type

- **Type:** Action Workflow (performs sync operations)
- **Trigger:** Manual only (no auto-sync, no watch mode)
- **Integration:** Standalone workflow (not callable from other workflows)

### 3. Architecture Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Tooling** | MCP-native | Use `mcp_beads_*` tools directly, no CLI shelling |
| **Interaction** | Menu-driven | User selects sync operations from menu |
| **Trigger** | Manual | User explicitly runs sync workflow |
| **Status Source of Truth** | Beads | Beads wins for status (open/in_progress/closed) |
| **Context Source of Truth** | Story Files | Stories win for context (AC, tasks, deps) |

### 4. MCP Tools Mapping

| Sync Operation | MCP Tool(s) |
|----------------|-------------|
| Init workspace | `mcp_beads_set_context` → `mcp_beads_init` |
| Create epic | `mcp_beads_create(issue_type='epic')` |
| Create story | `mcp_beads_create(issue_type='feature')` |
| Add dependency | `mcp_beads_dep(dep_type='blocks')` |
| Link parent-child | `mcp_beads_dep(dep_type='parent-child')` |
| Check status | `mcp_beads_list` or `mcp_beads_show` |
| Update progress | `mcp_beads_update(status='in_progress')` |
| Complete task | `mcp_beads_close(reason='Completed')` |
| Find ready work | `mcp_beads_ready` |
| Validate health | `mcp_beads_validate` |
| Fix orphans | `mcp_beads_repair_deps(fix=true)` |

### 5. Story File Modification

**Before sync:**
```markdown
# Story US-012: User Authentication

## Status: Draft
## Dependencies
- US-010 must be complete
```

**After sync (beads_id injected):**
```markdown
---
beads_id: bd-a3f8e9.1
beads_parent: bd-a3f8e9
last_synced: 2025-12-17T10:13:52+07:00
---

# Story US-012: User Authentication

## Status: in_progress
## Dependencies
- US-010 must be complete
```

### 6. Sync Operations Menu

```
┌─────────────────────────────────────────────────────────────┐
│ BMAD-BEADS SYNC WORKFLOW                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  [I] Init Sync    - First-time BMAD → Beads load            │
│  [P] Push         - Story updates → Beads                   │
│  [L] Pull         - Beads status → Story files              │
│  [S] Status       - Show sync status & diff                 │
│  [D] Doctor       - Validate consistency                    │
│  [X] Exit         - End workflow                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 7. Doctor Behavior

- Report issues first (not auto-fix)
- After report, ask user: "Auto-fix via workflow or manual fix?"
- If auto-fix: execute `mcp_beads_repair_deps(fix=true)` and update story files
- If manual: provide list of issues for user to fix themselves

### 8. Success Criteria

- Story files and Beads database remain in sync
- Agents can query `mcp_beads_ready` to find next work
- Status changes in Beads reflect in story file Status field
- New stories automatically get `beads_id` on push
- Doctor command catches drift before it causes problems

---

## Workflow Structure Design (Approved)

### Step Overview

| Step | File | Purpose |
|------|------|---------|
| 1 | `step-01-init.md` | Initialize context, detect sync state, scan stories |
| 2 | `step-02-sync-menu.md` | Main menu loop - execute sync operations |
| 3 | `step-03-exit.md` | Report summary, show stats, end workflow |

### File Structure

```
bmad-custom-src/workflows/bmad-beads-sync/
├── workflow.md
├── workflow-plan-bmad-beads-sync.md
└── steps/
    ├── step-01-init.md
    ├── step-02-sync-menu.md
    └── step-03-exit.md
```

### Role & Persona

- **Role:** Sync Operations Specialist
- **Tone:** Direct, action-oriented, concise
- **Communication:** Report what was done, what changed, any issues
- **Collaboration Level:** Low - mostly autonomous with status reports

### Continuation Support

❌ Not needed - each operation is atomic, menu loop handles session flow

---

## Build Summary (Step 7)

### Generated Files

| File | Path | Description |
|------|------|-------------|
| `workflow.md` | `bmad-custom-src/workflows/bmad-beads-sync/workflow.md` | Main workflow entry |
| `step-01-init.md` | `bmad-custom-src/workflows/bmad-beads-sync/steps/step-01-init.md` | Initialization |
| `step-02-sync-menu.md` | `bmad-custom-src/workflows/bmad-beads-sync/steps/step-02-sync-menu.md` | Main menu loop |
| `step-03-exit.md` | `bmad-custom-src/workflows/bmad-beads-sync/steps/step-03-exit.md` | Exit & summary |
| `custom.yaml` | `bmad-custom-src/custom.yaml` | Workflow registration |

### Build Date

2025-12-17

### Next Steps

1. Test the workflow by running `/bmad-beads-sync`
2. Verify Beads MCP server is connected
3. Ensure story files exist in `docs/stories/`
4. Run Init Sync to create initial Beads issues

---

## Final Review (Step 8)

### Validation Results

| Check | Status | Notes |
|-------|--------|-------|
| Configuration validation | ✅ PASSED | workflow.md has correct structure |
| Step compliance | ✅ PASSED | All steps follow template |
| Cross-file consistency | ✅ PASSED | Variables consistent across files |
| Requirements verification | ✅ PASSED | All approved requirements implemented |
| File sizes | ✅ PASSED | All files <10KB |

### Completion Checklist

- [x] All files generated successfully
- [x] No syntax errors in YAML frontmatter
- [x] All paths correctly configured
- [x] Variables consistent
- [x] Design requirements met (MCP-native, menu-driven, bidirectional sync)
- [x] Best practices followed

### Files Created

| File | Size | Lines |
|------|------|-------|
| `workflow.md` | 2.2 KB | ~55 lines |
| `step-01-init.md` | ~4 KB | ~120 lines |
| `step-02-sync-menu.md` | ~8 KB | ~220 lines |
| `step-03-exit.md` | ~3 KB | ~100 lines |

### How to Use

1. **Invoke the workflow:** Run `/bmad-beads-sync` in a new conversation
2. **Follow initialization:** Workflow auto-sets Beads context and scans stories
3. **Use the menu:** Select sync operations as needed
4. **Exit when done:** Final stats are displayed

### Compliance Check

To validate this workflow meets BMAD standards, run in a new context:
```
/bmad-bmb-workflows-workflow-compliance-check
```
Provide path: `bmad-custom-src/workflows/bmad-beads-sync/workflow.md`

---

## Workflow Creation Complete ✅

**Status:** APPROVED AND READY FOR USE

**Created:** 2025-12-17

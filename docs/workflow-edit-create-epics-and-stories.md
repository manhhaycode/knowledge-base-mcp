---
stepsCompleted: [1]
targetWorkflow: create-epics-and-stories
userGoal: Integrate beads MCP for tracking epics/stories/issues
createdDate: 2025-12-16
---

# Workflow Edit: create-epics-and-stories + create-story

## User's Goal

Integrate **beads MCP** issue tracking into the epic/story creation workflows to automatically track epics, stories, and sprints when they are created or changed.

---

## Workflow Analysis

### Target Workflows

| Property | create-epics-and-stories | create-story |
|----------|-------------------------|--------------|
| **Path** | `.bmad/bmm/workflows/3-solutioning/create-epics-and-stories` | `.bmad/bmm/workflows/4-implementation/create-story` |
| **Format** | Standalone (New) | Legacy (XML) |
| **Total Steps** | 4 | 6 (within XML) |
| **Step Flow** | Sequential with menu halts | Sequential with checks |

### Structure Analysis

#### create-epics-and-stories (Standalone Format)
- **Type**: Document-generating interactive workflow
- **Files**:
  - `workflow.md` - Main workflow configuration
  - `steps/step-01-validate-prerequisites.md` - Validates PRD/Architecture exist
  - `steps/step-02-design-epics.md` - Designs epic structure ⭐ **Integration point**
  - `steps/step-03-create-stories.md` - Creates individual stories ⭐ **Integration point**
  - `steps/step-04-final-validation.md` - Final validation
  - `templates/` - Output templates

#### create-story (Legacy XML Format)
- **Type**: Context-generation action workflow
- **Files**:
  - `workflow.yaml` - Configuration and variables
  - `instructions.xml` - Main workflow logic
  - `template.md` - Story output template
  - `checklist.md` - Validation checklist

### Content Characteristics

- **Purpose**: Create developer-ready stories with comprehensive context
- **Instruction Style**: Collaborative facilitation with user approval gates
- **User Interaction**: Menu-driven with halt points
- **Output**: `epics.md`, individual story files, `sprint-status.yaml` updates

---

## Current Tracking Mechanism

Both workflows currently update **`sprint-status.yaml`** for status tracking:
- Epic status: `backlog` → `in-progress` → `done`
- Story status: `backlog` → `ready-for-dev` → `in-progress` → `done`

---

## Beads MCP Integration Points

### Available beads MCP Tools

| Tool | Purpose | Integration Use Case |
|------|---------|---------------------|
| `mcp_beads_create` | Create new issue (bug, feature, task, epic, chore) | Create epic/story in beads when generated |
| `mcp_beads_update` | Update issue status, priority, assignee | Sync status changes |
| `mcp_beads_list` | List issues with filters | Check existing issues |
| `mcp_beads_dep` | Add dependencies between issues | Link stories to epics, story dependencies |
| `mcp_beads_show` | Show issue details | Retrieve existing issue info |
| `mcp_beads_close` | Close/complete an issue | Mark completed |

### Proposed Integration Points

#### Workflow 1: create-epics-and-stories

**Step 2: Design Epics** (step-02-design-epics.md)
- After epic approval → Create epic in beads with `mcp_beads_create`
- Issue type: `epic`
- Include FR coverage as description

**Step 3: Create Stories** (step-03-create-stories.md)
- After each story approval → Create story in beads with `mcp_beads_create`
- Issue type: `task` or `feature`
- Link to parent epic with `mcp_beads_dep` (parent-child)
- Include acceptance criteria

#### Workflow 2: create-story

**Step 5: Create Story File** (instructions.xml)
- After story file created → Either update existing or create new beads issue
- Sync story status to beads

**Step 6: Update Sprint Status** (instructions.xml)
- Sync status change to beads tracker

---

## Initial Assessment

### Strengths
- Clear step separation allows precise integration points
- Menu-driven flow provides natural pause points for beads sync
- Both workflows already track status (can mirror to beads)
- Template-driven output makes extracting issue content straightforward

### Potential Issues
- Legacy XML format (create-story) requires careful modification
- Need to decide: beads as primary or secondary tracker (with sprint-status.yaml)
- ID synchronization between sprint-status.yaml keys and beads issue IDs
- Error handling if beads MCP unavailable

### Implementation Considerations
1. **Dual-tracking vs. Migration?** - Keep sprint-status.yaml AND beads, or migrate?
2. **Epic ID mapping** - How to link `epic-1` in sprint-status.yaml to beads issue ID?
3. **Story ID format** - Match `1-2-story-name` pattern to beads?

---

_Analysis completed on 2025-12-16_

---

## Improvement Goals

### User Decisions

1. **Tracking Strategy**: Dual-tracking (keep `sprint-status.yaml` AND sync to beads)
2. **Scope**: Modify BOTH workflows (`create-epics-and-stories` + `create-story`)
3. **ID Strategy**: Hash-based IDs with custom prefix (e.g., `kbm-a1b2`)

### Beads Configuration

Beads supports custom prefixes via `.beads/config.yaml`:
```yaml
# Issue prefix for this repository
issue-prefix: "kbm"  # Creates issues like "kbm-a1b2c3"
```

### Priority: CRITICAL Improvements

1. **Create epics in beads** when epics are approved (step-02-design-epics.md)
2. **Create stories in beads** when stories are generated (step-03-create-stories.md)
3. **Link stories to parent epics** using beads dependencies
4. **Sync story status** when `create-story` workflow updates sprint-status.yaml

### Priority: IMPORTANT Enhancements

1. **Store beads issue ID** in story markdown files (in frontmatter)
2. **Use `external_ref`** to store story key format (e.g., `1-2-user-auth`)
3. **Dual-write**: Update both sprint-status.yaml AND beads on status changes

### Priority: NICE-TO-HAVE

1. Check for existing beads issues before creating duplicates
2. Sync existing issues if re-running workflows

---

_Goals documented on 2025-12-16_

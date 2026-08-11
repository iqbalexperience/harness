---
name: harness
description: "The team-architecture factory for AI agents — turns a domain description into an agent team and their skills for any project. Use when asked to 'build a harness', 'design an agent team', 'harness engineering', 'scaffold agents and skills', or when auditing and extending existing multi-agent harnesses."
---

# Harness — Agent Team & Skill Architect (Open Agent Skills Ecosystem)

A meta-skill that turns a single-sentence domain description into a coordinated agent team and the procedural skills they use.

**Core Principles:**
1. **Scaffold Agent Definitions & Skills:** Generate agent role definitions (`.agents/agents/` or `.claude/agents/`) and procedural skills (`.agents/skills/` or `.claude/skills/`).
2. **Prioritize Agent Teams:** Default to multi-agent team mode for collaborative task execution with direct peer communication and shared task lists.
3. **Register Harness Pointer in AGENTS.md / CLAUDE.md:** Keep context lean by recording only minimal pointers (trigger rules + change log) in `AGENTS.md` (or `CLAUDE.md`).
4. **Continuous Harness Evolution:** Treat harnesses as evolving systems that adapt based on execution feedback, updating agents, skills, and documentation iteratively.

## Workflow

### Phase 0: Audit & Current State Assessment

When triggered, inspect the existing harness configuration first:

1. Read `project/.agents/agents/` (or `.claude/agents/`), `project/.agents/skills/` (or `.claude/skills/`), and `project/AGENTS.md` (or `CLAUDE.md`).
2. Branch execution based on current state:
   - **New Scaffold:** Agent/skill directories missing or empty → Run full pipeline from Phase 1.
   - **Extension:** Existing harness found and new agents/skills requested → Execute required phases based on the Phase Selection Matrix below.
   - **Operations & Maintenance:** Requests to audit, fix, or sync existing harness → Jump to Phase 7-5 Operations & Maintenance workflow.

   **Phase Selection Matrix for Extension:**
   | Change Type | Phase 1 | Phase 2 | Phase 3 | Phase 4 | Phase 5 | Phase 6 |
   |-------------|---------|---------|---------|---------|---------|---------|
   | Add Agent | Skip | Placement only | Required (inc. 3-0) | If dedicated skill needed | Update Orchestrator | Required |
   | Add/Modify Skill | Skip | Skip | Skip | Required (inc. 4-0) | If connection changes | Required |
   | Change Architecture | Skip | Required | Affected agents only | Affected skills only | Required | Required |

3. Detect drift between agent/skill files and pointer logs in `AGENTS.md` / `CLAUDE.md`.
4. Report audit summary to the user and confirm execution plan.

### Phase 1: Domain Analysis
1. Extract domain and project scope from user request.
2. Identify core task categories (generation, verification, editing, analysis, deployment).
3. Analyze potential conflicts or duplicates with existing agents/skills based on Phase 0 audit.
4. Explore project codebase — technology stack, data models, major modules.
5. **Detect User Skill Level:** Observe language and terminology usage to tailor communication tone. Avoid unexplained technical jargon for non-technical users.

### Phase 2: Team Architecture Design

#### 2-1. Select Execution Mode

**Agent Teams is the primary default.** When 2 or more agents collaborate, evaluate agent teams first. Team members communicate directly via `SendMessage` and coordinate via shared task boards (`TaskCreate`).

| Mode | When to Use | Key Characteristics |
|------|-------------|---------------------|
| **Agent Team** (Default) | 2+ agents collaborating, real-time feedback/coordination needed | Self-coordinating via messaging & task list |
| **Subagents** (Alternative) | Single-agent task, returning results to main is sufficient | Direct `Agent` tool invocation, parallel background execution |
| **Hybrid** | Phases differ in requirements (e.g., parallel collection → consensus integration) | Mix team and subagent modes per Phase |

> For detailed decision trees and pattern comparisons, see `references/agent-design-patterns.md`.

#### 2-2. Select Architecture Pattern

1. Decompose workload into specialized domains.
2. Select team architecture pattern (refer to `references/agent-design-patterns.md`):
   - **Pipeline:** Sequential dependent tasks
   - **Fan-out / Fan-in:** Parallel independent tasks
   - **Expert Pool:** Context-driven dynamic selection
   - **Producer-Reviewer:** Generation followed by QA validation
   - **Supervisor:** Central manager orchestrating state & dispatch
   - **Hierarchical Delegation:** Top-down recursive delegation

#### 2-3. Agent Separation Criteria

Evaluate across 4 axes: Expertise, Parallelism, Context Boundaries, and Reusability.

### Phase 3: Agent Definition Generation

#### 3-0. Duplicate Agent Review

Before creating new agents, check existing files in `.agents/agents/` or `.claude/agents/` to prevent redundant role duplication.

**Every agent MUST be defined in `.agents/agents/{name}.md` or `.claude/agents/{name}.md`.** Direct role prompts in tool calls without definition files are prohibited.

Required file sections: Core Role, Operating Principles, Input/Output Protocols, Error Handling, and Collaboration Contracts. For Agent Teams mode, include `## Team Communication Protocol`.

**QA Agent Requirements:**
- QA agents should use general-purpose capabilities capable of running verification scripts.
- Focus on cross-boundary verification — comparing API contracts directly against consumer implementations.
- Execute incremental QA after completing each module rather than a single check at the end.
- See `references/qa-agent-guide.md` for detailed guidelines.

### Phase 4: Skill Generation

Create procedural skills for agents under `.agents/skills/{name}/SKILL.md` (or `.claude/skills/{name}/SKILL.md`).

#### 4-0. Duplicate Skill Review
Inspect `.agents/skills/` or `.claude/skills/` to prevent duplicate capability definitions.

#### 4-1. Skill Directory Layout

```text
skill-name/
├── SKILL.md (Required: YAML frontmatter + markdown body)
└── Bundled Resources (Optional)
    ├── scripts/    - Deterministic execution scripts
    ├── references/ - Documents loaded on demand
    └── assets/     - Templates, images, or static files
```

#### 4-2. Description Writing — Pushy Triggers
Descriptions are the sole trigger mechanism for skills. Write **pushy, active descriptions** detailing exact trigger conditions and near-miss exclusions.

#### 4-3. Content Design Principles
- **Explain the Why:** Provide underlying rationale instead of rigid rules so agents handle edge cases intelligently.
- **Keep it Lean:** Aim for <500 lines in `SKILL.md`. Offload detailed policies to `references/`.
- **Generalize:** Focus on general principles over single hyper-specific examples.
- **Bundle Repeated Code:** Place reusable scripts in `scripts/`.

#### 4-4. Progressive Disclosure (3-Tier Loading)
- **Tier 1: Metadata (YAML):** Always present (~100 words).
- **Tier 2: SKILL.md Body:** Loaded on trigger (<500 lines).
- **Tier 3: references/:** Loaded conditionally when required.

### Phase 5: Integration & Orchestration

Scaffold an orchestrator skill connecting agents, data passing protocols, and error fallback handlers.

#### 5-0. Data Passing Protocols
- **Task Board:** Shared status and task dependency management.
- **File-based:** Inter-agent artifacts written to `_workspace/{phase}_{agent}_{artifact}.{ext}`.
- **Message-based:** Real-time peer messaging for coordination.

#### 5-1. AGENTS.md / CLAUDE.md Pointer Registration
Register minimal pointers (trigger rules + change log) in `AGENTS.md` (or `CLAUDE.md`). Keep detailed agent/skill listings inside definition files to avoid duplication.

```markdown
## Harness: {Domain Name}

**Goal:** {One-line description of harness objective}

**Trigger:** Use `{orchestrator-skill-name}` when handling tasks related to {Domain}.

**Change Log:**
| Date | Change | Target | Reason |
|------|--------|--------|--------|
| {YYYY-MM-DD} | Initial Scaffold | All | - |
```

### Phase 6: Validation & Testing

Validate generated harness components:
1. **Structure Check:** File paths, frontmatter schema, cross-references.
2. **Trigger Verification:** Test 8-10 Should-trigger and 8-10 Should-NOT-trigger near-miss queries.
3. **Execution & Baseline Comparison:** Run with-skill vs without-skill comparison tests.
4. **Dry-run Test:** Verify workflow logic, data passing links, and fallback handling.

### Phase 7: Harness Evolution

Harnesses adapt based on execution feedback:
- Collect post-execution user feedback.
- Log architectural modifications in the Change Log.
- Refine skill descriptions, agent responsibilities, or workflow order based on recurring failure patterns.

---

## Deliverables Checklist

- [ ] `.agents/agents/` (or `.claude/agents/`) — Agent definition files created
- [ ] `.agents/skills/` (or `.claude/skills/`) — Skill packages created (`SKILL.md` + `references/`)
- [ ] 1 Orchestrator Skill with workflow, data passing, error handling, and test scenarios
- [ ] Explicit execution mode selected (Agent Team / Subagents / Hybrid)
- [ ] Duplicate agent & skill checks completed (Phases 3-0 & 4-0)
- [ ] Pushy trigger descriptions written with follow-up keywords
- [ ] `SKILL.md` body kept under 500 lines (offloaded to `references/` if larger)
- [ ] Pointer registered in `AGENTS.md` or `CLAUDE.md` (trigger rules + change log)
- [ ] Change log updated with revision history

## References

- Architectural Patterns: `references/agent-design-patterns.md`
- Team Examples: `references/team-examples.md`
- Orchestrator Templates: `references/orchestrator-template.md`
- Skill Authoring Guide: `references/skill-writing-guide.md`
- Skill Testing Guide: `references/skill-testing-guide.md`
- QA Agent Guide: `references/qa-agent-guide.md`

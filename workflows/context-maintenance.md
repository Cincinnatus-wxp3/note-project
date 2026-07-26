# Context Maintenance Workflow

This is a personal workflow summary distilled from real embedded development,
not a universal engineering process. Adapt it to the current project's verified
facts, tools, and risk boundaries.

Personal baseline: `Requirement or Problem → Analysis → Context Change → Validation`.

Use this workflow after a task iteration, SDK/toolchain change, board revision or discovery that invalidates a rule.

Long-lived context is executable input for future AI work. Keep it smaller and stricter than a project diary.

`Evidence Input → AI Extraction → Scoped Context Change → Consistency Validation → Future Reuse`

## 1. Input

Require:

- source revision and worktree state;
- task contract and acceptance-criterion statuses;
- exact Build and Hardware states;
- raw evidence paths inside the authorized boundary;
- MCU/board/SDK/toolchain scope;
- intended target context file;
- invalidation conditions.

This workflow consumes evidence. It does not create new hardware facts.

## 2. AI Analysis

For each candidate rule:

1. separate stable instruction from one-off incident detail;
2. identify the acceptance criterion and evidence scope;
3. distinguish source, host-test, build and hardware evidence;
4. preserve the source task’s Build and Hardware states;
5. identify hidden assumptions;
6. define when the rule becomes stale;
7. reject unsupported generalization.

A hardware-dependent rule can be `Verified` only when direct evidence exists and the source task has `HARDWARE PASSED`.

## 3. Context Change

1. read the original context, task contract and raw evidence;
2. inspect current project-local instructions for conflicts;
3. write the smallest executable rule;
4. link rather than copy raw evidence;
5. remove or downgrade stale conflicting rules;
6. review for secrets, private paths and unsupported claims;
7. re-read the result as if starting a new task.

Rule format:

```yaml
rule:
  id: "stable-id"
  status: "Verified | Implemented, not rerun | Blocked"
  applies_to:
    project: ""
    board_revision: ""
    sdk: ""
    toolchain: ""
  instruction: "one executable rule"
  acceptance_criterion: ""
  evidence_scope: "source | host-test | build | hardware"
  source_task:
    revision: ""
    record_path: ""
    build_state: "BUILD PASSED | BUILD FAILED | BUILD NOT RUN"
    hardware_state: "HARDWARE PASSED | HARDWARE FAILED | HARDWARE NOT RUN"
  invalidated_by: []
```

## 4. Validation

For documentation-only maintenance, report:

- Build state: `BUILD NOT RUN`
- Build reason: `documentation-only maintenance`
- Hardware state: `HARDWARE NOT RUN`
- Hardware reason: `no hardware criterion in this maintenance task`

If the change modifies a build script, generated configuration or source-controlled command, run the relevant build workflow.

Accept the context change only when:

- it does not contradict current code or hardware;
- another task can apply it without hidden knowledge;
- scope and invalidation conditions are visible;
- it cannot trigger a destructive action without approval;
- evidence remains locatable;
- no restricted information is present.

## 5. Result and Future Reuse

Place:

- repository-wide execution rules in root `AGENTS.md`;
- hardware facts near the board/project context;
- build commands near the build system;
- interface rules near the owning module;
- incident history in evidence records, not Agent context.

If validation fails, keep the candidate in the task record rather than permanent context.

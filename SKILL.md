---
name: develop-embedded-projects-with-ai
description: Use a personal task template distilled from real AI-assisted embedded development. Turn MCU and board context plus firmware requirements into bounded AI analysis, repository changes, build evidence, hardware validation, failure closure, and reusable project context. Apply it when analyzing or changing embedded projects, drivers, protocols, SDKs, build systems, board Bring-up, hardware debugging, or Codex/ChatGPT-assisted firmware tasks.
---

# Personal AI-Assisted Embedded Development Task Template

Use this skill as a personal task template:

`Input → Process → Output`

Here, `Process` means AI-assisted analysis and execution constrained by verified
engineering context, tool results, and human authorization.

Structure applicable engineering tasks through:

`Hardware Context → AI Reasoning → Tool Execution → Build → Hardware Validation`

Treat this sequence as a development method. Treat AI output as an unverified
proposal until the required build and hardware gates produce evidence. Apply
[AGENTS.md](AGENTS.md) as experience-derived working constraints. This template
summarizes one developer's engineering practice; it is not a complete Agent
system, an industry standard, or a production framework.

## Collect task inputs

Collect or inspect the minimum input required for the next safe action.

### Engineering request

- Goal and user-observable behavior.
- Problem or requirement source.
- In-scope and out-of-scope modules.
- Compatibility and interface constraints.
- Acceptance criteria and permitted mutations.

### Hardware context

- Exact MCU part number, board and revision.
- Supply and I/O voltage, clock source, pin and peripheral ownership.
- External devices, bus topology, reset and boot conditions.
- Schematic, pin map, datasheet, reference manual, errata, or verified
  observation supporting each relevant fact.
- Programmer, debugger, serial connection, instruments, and recovery path.

### Project context

- Repository root, current revision, worktree state, and local instructions.
- SDK/HAL and version, toolchain, build system, target, and build command.
- Project tree, initialization path, linker layout, generated-code boundaries,
  dependencies, tests, and known baseline failures.
- Architecture, execution model, memory limits, timing limits, safety limits,
  and confidentiality boundary.

Mark missing information as `Unknown`. Never complete a hardware fact from
model memory or a similar board. Continue only with work that does not depend
on the missing fact.

## Describe expected task outputs

Return these artifacts at the highest evidenced stage:

1. Hardware and project context snapshot.
2. Requirement and acceptance-criterion map.
3. Bounded change plan and affected-layer map.
4. Repository changes or a precise blocker.
5. Tool execution record.
6. Build state and build evidence.
7. Hardware state and hardware evidence.
8. Failure summary with evidence and the next safe action when any gate fails.
9. Verified context worth preserving.
10. Smallest safe next action.

Do not claim an artifact, command, observation, or result that was not produced
in the current environment or supplied as traceable evidence.

## Apply task stages

### 1. Project Analysis

**Input**

- MCU and board identity.
- SDK/HAL and version.
- Repository tree and build files.
- Linker, configuration, startup, generated files, and local instructions.

**Process**

1. Inspect repository instructions and version-control state.
2. Trace initialization, module dependencies, hardware binding, and build
   entry points.
3. Identify toolchain, target configuration, generated-code ownership, and
   existing verification paths.
4. Classify the resource level and system profile using `AGENTS.md`, or use
   `N/A` when the task has no MCU/firmware system scope.
5. Separate verified facts, assumptions, conflicts, and unknowns.
6. Run or record the smallest representative baseline when authorized and
   possible.

**Output**

- Project context snapshot.
- Architecture and dependency summary.
- MCU resource class, system profile, and peripheral-ownership summary, or
  `N/A` where the task has no MCU scope.
- Baseline evidence or reason it was not run.
- Risk, unknown, and blocker list.
- Initial modification boundary.

### 2. Requirement Decomposition

**Input**

- Original problem or feature request.
- Project Analysis output.
- User scenarios, constraints, failure reports, and desired observations.

**Process**

1. Translate the request into user-visible or system-visible behavior.
2. Separate functional behavior, engineering constraints, error behavior, and
   operational recovery.
3. Map each requirement to an existing layer, module, interface, or new
   bounded responsibility.
4. Define non-goals and compatibility boundaries.
5. Define acceptance criteria for host checks, build evidence, and hardware
   observations.
6. Identify which facts must be supplied before implementation.

**Output**

- Task contract.
- Requirement-to-module map.
- Acceptance-criterion table.
- Explicit assumptions and unresolved questions.
- Validation ladder and rollback or recovery requirement.

Use ChatGPT only to refine requirements, functional logic, architecture
options, algorithms, parameters, and documentation when its output is
available. Treat that material as an unverified reasoning input. Use Codex to
reconcile it with the actual repository, tools, and hardware evidence.

### 3. Architecture and Implementation

**Input**

- Approved task contract.
- Verified project and hardware context.
- Existing interfaces and implementation patterns.

**Process**

1. Select the smallest execution model justified by concurrency, timing, and
   memory evidence.
2. Preserve the downward dependency direction:
   `Application → Middleware → Driver → BSP → Hardware`.
3. Define ownership for peripherals, buffers, tasks, interrupts, state, and
   error recovery.
4. Represent persistent lifecycle behavior with an explicit FSM.
5. Separate communication transport, framing, protocol, service, and
   application responsibilities.
6. Define timeout, retry, backoff, reconnect, degraded mode, and terminal
   failure behavior.
7. Search definitions, call sites, configuration, and generated boundaries
   before editing.
8. Apply the smallest in-scope change that produces one observable result.
9. Avoid speculative refactoring, unbounded allocation, hidden state, and
   unrelated formatting.

**Output**

- Change design and affected-layer map.
- Execution-model rationale.
- Memory, concurrency, state-machine, communication, and recovery decisions.
- Changed-file list and preserved interfaces.
- Remaining assumptions and risks.

### 4. Build Verification

**Input**

- Exact target and configuration.
- Recorded build command and environment.
- Changed revision or worktree state.

**Process**

1. Run applicable generation, formatting, static analysis, and host tests.
2. Build the exact configured target.
3. Preserve the first causal error, exit result, warnings, map or size output,
   and artifact identity.
4. Compare failures and resource impact with the baseline.
5. Return build failures to Project Analysis, Requirement Decomposition, or
   Architecture and Implementation according to the evidence.

**Output**

- Exact command and relevant tool versions.
- Target, configuration, exit status, and warnings.
- Artifact path and identity when produced.
- Flash/RAM or section impact when relevant.
- Exactly one Build state.

Never flash an artifact that did not pass the required Build gate.

### 5. Hardware Validation

**Input**

- Confirmed board, MCU, revision, voltage, power, and connections.
- Exact built artifact and target address or partition.
- Acceptance criteria, debugger or serial configuration, recovery path, and
  explicit approval for each state-changing action.

**Process**

1. Verify target identity and artifact identity before mutation.
2. Request approval for erase, flash, OTA, state-losing reset, halt,
   memory/register write, option-byte or fuse change, calibration write, or
   boot-region change.
3. Execute only the approved action with the recorded parameters.
4. Capture serial, debugger, fault-register, memory, peripheral, or external
   instrument evidence as applicable.
5. Exercise startup, normal behavior, boundary conditions, error behavior,
   recovery, and relevant regression scenarios.
6. Compare every observation with a named acceptance criterion.

**Output**

- Target and firmware identity.
- Exact action and parameters.
- Raw or minimally processed observations.
- Criterion-by-criterion result.
- Exactly one Hardware state.

Compilation, simulation, static review, and expected behavior never satisfy a
hardware-dependent acceptance criterion.

### 6. Failure Closure

**Input**

- Failed criterion.
- Build output, logs, debugger state, measurements, or reproducible user
  observations.
- Baseline and latest change boundary.

**Process**

1. Preserve raw evidence before changing the system.
2. Locate the earliest failed stage in the five-stage loop.
3. Distinguish pre-existing failure, introduced failure, and unknown
   attribution.
4. Form the smallest testable hypothesis from evidence.
5. Apply one bounded correction and rerun from the earliest affected stage.
6. Stop retries when evidence no longer discriminates between hypotheses.

**Output**

- Failure stage and reproduction conditions.
- First actionable error or observation.
- Hypothesis, correction, and rerun evidence.
- Residual risk and next required fact or tool.
- Updated Build and Hardware states.

### 7. Context Preservation

**Input**

- Facts verified from source, build, or hardware.
- Commands and tool versions that actually worked.
- Stable architecture decisions, recurring failures, and recovery knowledge.

**Process**

1. Preserve only reusable, current, and traceable information.
2. Separate fact, assumption, historical note, and illustrative material.
3. Remove credentials, private endpoints, customer identifiers, proprietary
   code, production logs, device identities, and confidential schematics.
4. Record source revision, target identity, evidence location, and date when
   those details affect validity.
5. Update existing context instead of duplicating conflicting guidance.

**Output**

- Project-local hardware context.
- Verified build, flash, debug, and validation commands.
- Known constraints, failure signatures, and recovery notes.
- Explicit stale or unresolved items.

## Apply mutation approval gates

Classify each intended action before execution.

| Mutation class | Authorization rule |
| --- | --- |
| Read-only inspection | Proceed within the user-provided scope unless access itself exposes restricted material. |
| Repository edit | Treat an explicit implementation request as approval only for bounded, in-scope edits. Inspection, explanation, review, or diagnosis does not authorize edits. |
| Broad repository change | Ask before architecture replacement, broad regeneration, license changes, dependency replacement, or edits outside agreed paths. |
| Environment change | Ask before installing or upgrading SDKs, toolchains, drivers, packages, services, global dependencies, or system configuration. |
| Device mutation | Ask before erase, flash, OTA, reset with state loss, halt, memory/register write, option-byte or fuse change, calibration write, bootloader change, partition change, or rollback. |
| Physical change | Ask before wiring, jumper, voltage, load, motor, relay, heater, high-current-path, or other actuation changes. Require the user to perform physical actions unless an authorized control tool exists. |

In every device or physical approval request, name the target, action,
artifact or setting, expected effect, risk, and recovery path. Never bundle a
previously approved action with a materially different mutation.

## Use exact evidence states

Assign one acceptance status to every criterion:

- `Verified`
- `Implemented, not rerun`
- `Planned`
- `Illustrative`
- `Blocked`

Assign exactly one Build state:

- `BUILD PASSED`: Run the required build and checks successfully for the
  stated target and revision.
- `BUILD FAILED`: Run a required build or check and observe a failure.
- `BUILD NOT RUN`: Do not run the build; state why.

Assign exactly one Hardware state:

- `HARDWARE PASSED`: Run the exact artifact on the confirmed target and
  satisfy all required hardware criteria.
- `HARDWARE FAILED`: Run validation and observe at least one failed hardware
  criterion.
- `HARDWARE NOT RUN`: Do not perform hardware validation; state why.

Use `HARDWARE FAILED`, not `HARDWARE NOT RUN`, when validation ran and failed.
Use these value markers consistently:

- `Unknown`: the current task was inspected, but the value could not be
  established.
- `[UNVERIFIED]`: a supplied or historical value exists but lacks current
  evidence.
- `[TO BE PROVIDED]`: a template or intake field has not yet been supplied.

These markers are not acceptance statuses. Never invent logs, measurements,
timings, counts, percentages, device behavior, or prior execution.

## Stop at the affected stage

Stop the unsafe or unverifiable action when:

- Hardware identity, revision, voltage, power, pinout, clock, memory map,
  address, partition, or protection state is missing or contradictory.
- Repository scope, user changes, generated-code ownership, target
  configuration, or permission is ambiguous.
- A required SDK, toolchain, dependency, probe, serial configuration,
  instrument, credential, or licensed source is unavailable.
- The connected target or selected artifact cannot be identified exactly.
- The baseline prevents failure attribution.
- The request violates verified electrical, timing, memory, safety, or
  confidentiality constraints.
- A required Build or Hardware criterion cannot run.

Continue safe independent inspection or host-side analysis. Preserve completed
evidence, name the blocked stage, state the exact missing fact or approval, and
request only what is required to continue.

## Route the task

Read only the workflow selected for the current task:

- Feature work: [workflows/feature-development.md](workflows/feature-development.md)
- Failure diagnosis: [workflows/bug-analysis.md](workflows/bug-analysis.md)
- Peripheral or driver work: [workflows/driver-development.md](workflows/driver-development.md)
- Board Bring-up: [workflows/hardware-bringup.md](workflows/hardware-bringup.md)
- Knowledge maintenance: [workflows/context-maintenance.md](workflows/context-maintenance.md)

A pure flash, OTA/FOTA, configuration-installation, or other device-mutation
request is a validation-only path, not feature development. Apply
[AGENTS.md](AGENTS.md) sections 7–9 and the
[Flash/program contract](references/tool-contracts.md#flashprogram-contract).
Do not perform the mutation until the exact target, artifact, risk,
recovery/rollback path, and explicit approval are all present.

Read methodology only when the corresponding decision is active:

- Context completeness: [methodology/hardware-context.md](methodology/hardware-context.md)
- Hallucination and unsupported-claim control: [methodology/ai-hallucination-control.md](methodology/ai-hallucination-control.md)
- Evidence and gate design: [methodology/engineering-validation.md](methodology/engineering-validation.md)
- Workflow friction and tool usability: [methodology/developer-experience.md](methodology/developer-experience.md)

Read practice documentation only when tracing or updating rules:

- Rule provenance: [docs/origin-of-rules.md](docs/origin-of-rules.md)
- Personal experience-to-rule index: [docs/experience-mapping.md](docs/experience-mapping.md)
- Practice design notes: [docs/design-philosophy.md](docs/design-philosophy.md)
- Experience-to-behavior mapping: [docs/mapping-to-practice.md](docs/mapping-to-practice.md)

Read supporting references only when needed:

- Experience-derived engineering patterns:
  [references/engineering-lessons.md](references/engineering-lessons.md)
- Experience provenance and confidentiality boundary:
  [references/project-context.md](references/project-context.md)
- Stateful tool requirements:
  [references/tool-contracts.md](references/tool-contracts.md)
- Evidence recording:
  [references/evidence-record.md](references/evidence-record.md)
- Final status assignment:
  [references/verification-checklist.md](references/verification-checklist.md)

## Report each iteration

Return:

1. Selected workflow and task stages.
2. Target, scope, facts, assumptions, unknowns, and task contract.
3. Architecture and implementation decisions.
4. Changed files and preserved interfaces.
5. Tool commands and actual results.
6. Criterion statuses and exact Build state.
7. Exact Hardware state and supporting observations.
8. Failure closure, remaining risk, blockers, and smallest safe next action.
9. Verified context preserved for future work.

Do not claim completion beyond the highest evidenced stage.

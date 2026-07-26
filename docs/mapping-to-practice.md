# Mapping Engineering Practice to Agent Constraints

This document traces how recurring embedded-engineering problems become
constraints that an AI Agent can apply. The mapping is intentionally about
problem patterns, not employer implementations, customer systems or claimed
project results.

For the direct index from personal project experience to `AGENTS.md` rules,
read [experience-mapping.md](experience-mapping.md). This document focuses on
the more general change in Agent behavior, workflow selection, and evidence.

## Mapping model

```text
Recurring engineering problem
  → reusable engineering judgment
  → enforceable Agent constraint
  → workflow and tool gate
  → reviewable evidence
```

A lesson is useful to the Agent only when it changes behavior: what context to
request, which design defaults to use, which tool to call, when to stop and
what evidence is required.

## Practice map

| Experience pattern | Reusable judgment | Constraint in `AGENTS.md` | Workflow / evidence |
|---|---|---|---|
| Industrial IoT systems connect multiple devices and protocol variants while links fail independently | Separate transport, framing, protocol, device profile and application behavior; model connection and recovery explicitly | [§5.3 Communication](../AGENTS.md#53-communication-and-streaming-protocols), [§5.4 State Machines](../AGENTS.md#54-state-machines), [§5.5 Fault Tolerance](../AGENTS.md#55-defensive-behavior-and-fault-tolerance) | Feature or bug workflow; transition logs, retry/backoff evidence and recovery observation |
| CAT1 and LoRa links can be delayed, unavailable or partially functional | Link state, session state and application delivery are not interchangeable; retries and offline storage need bounds | [§4 Execution Model](../AGENTS.md#4-architecture-and-execution-model-selection), [§5.3](../AGENTS.md#53-communication-and-streaming-protocols), [§5.4](../AGENTS.md#54-state-machines) | Failure reproduction, communication traces and bounded-recovery checks |
| MCU ecosystems differ in SDK maturity, startup code, linker layout, examples and tooling | Treat the current part, board, SDK and build files as facts; isolate adaptation and classify resource limits before choosing architecture | [§1 Preflight](../AGENTS.md#1-preflight), [§2 Hardware Truth](../AGENTS.md#2-hardware-truth-boundary), [§3 Resource Class](../AGENTS.md#3-resource-class-and-system-profile) | Bring-up or driver workflow; native build, map file, debugger and peripheral evidence |
| Repeated environment setup, project creation, unreadable build errors and FOTA steps create development friction | Improve the path from intent to evidence; automate only repeatable steps and preserve the real command, version and failure output | [§7 Tool Execution](../AGENTS.md#7-tool-execution-rules), [§8 Build Gate](../AGENTS.md#8-build-gate), [§11 Completion Report](../AGENTS.md#11-completion-report-template) | Tool contracts, reproducible command records, build evidence and explicit mutation approval |
| Board Bring-up can look correct in source while clock, pin, power or peripheral behavior is wrong | Establish a known-low-risk baseline, validate one observable at a time and do not infer hardware behavior from compilation | [§2 Hardware Truth](../AGENTS.md#2-hardware-truth-boundary), [§6 AI Reasoning](../AGENTS.md#6-ai-reasoning-checklist), [§9 Hardware Validation](../AGENTS.md#9-hardware-validation-gate) | Bring-up workflow; exact target identity, probe output, serial/debugger observation and measurements |
| BootLoader and OTA work can alter persistent device state and create recovery risk | Partition, image identity, compatibility, rollback and recovery are part of the task contract; mutation needs explicit authority | [§2 Hardware Truth](../AGENTS.md#2-hardware-truth-boundary), [§7 Tool Execution](../AGENTS.md#7-tool-execution-rules), [§9 Hardware Validation](../AGENTS.md#9-hardware-validation-gate) | Feature workflow plus mutation gate; artifact hash, flash record, boot result and recovery-path evidence |
| AI Coding is useful for requirement refinement, project reading, bounded implementation and diagnosis, but can guess versions or hardware facts | Separate facts, assumptions and unknowns; give the Agent real project context; close the loop with tools, build and hardware feedback | [§0 Working Loop](../AGENTS.md#0-scope-and-working-loop), [§6 AI Reasoning](../AGENTS.md#6-ai-reasoning-checklist), [§10 Stop Conditions](../AGENTS.md#10-stop-conditions) | Selected task workflow; source references, actual commands and separate Build/Hardware states |

## What changes in Agent behavior

The mapping above causes the Agent to:

1. inspect the actual project before proposing a replacement architecture;
2. request exact MCU, board, SDK and toolchain identity before relying on APIs;
3. classify resources and concurrency before choosing polling, interrupts,
   event loops or an RTOS;
4. represent communication and recovery as states with bounded transitions;
5. use project-native build, flash and debug entry points;
6. keep build evidence separate from device evidence;
7. stop only the affected unsafe stage and name the missing fact;
8. write verified context back for future work without promoting assumptions
   to facts.

## Relationship to personal experience

The direct mapping from personal project experience to these rules is recorded
in [experience-mapping.md](experience-mapping.md). This repository does not
depend on Demo projects to establish results; current build and hardware claims
still require evidence from the actual task.

## Evidence and confidentiality boundary

The source experiences establish why a rule exists; they are not evidence that
the rule has passed in a new project. Current-task evidence still requires the
actual source revision, command output, build artifact and, when applicable,
hardware observation.

This repository therefore retains only generalized patterns. It excludes
employer or customer source, private schematics, protocol details, production
logs, identities, credentials, commercial data and unsupported metrics.

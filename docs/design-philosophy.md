# Design Notes for Reliable AI-Assisted Embedded Work

This document summarizes why an AI Agent needs explicit engineering
constraints before changing a firmware project. The notes come from personal
embedded-development practice. They are neither a formal framework nor a claim
that AI can develop embedded products autonomously.

## Contents

- [1. Constraints before generation](#1-constraints-before-generation)
- [2. Facts, assumptions and unknowns have different authority](#2-facts-assumptions-and-unknowns-have-different-authority)
- [3. Freedom should follow risk](#3-freedom-should-follow-risk)
- [4. Prefer deterministic, inspectable behavior](#4-prefer-deterministic-inspectable-behavior)
- [5. Verification is progressive](#5-verification-is-progressive)
- [6. Stop conditions are part of the design](#6-stop-conditions-are-part-of-the-design)
- [7. Context becomes reusable only after verification](#7-context-becomes-reusable-only-after-verification)
- [8. The intended boundary](#8-the-intended-boundary)

## 1. Constraints before generation

Code generation is only one step in an embedded task. A syntactically valid
change can still select the wrong peripheral instance, conflict with DMA,
violate an electrical limit, overwrite a generated-code region, or build for
the wrong target.

The Agent therefore establishes the operating boundary first:

- confirmed hardware and software facts;
- assumptions that still need evidence;
- unknowns that block only the affected action;
- allowed files, tools, targets and device mutations;
- separate Build and Hardware acceptance criteria.

The purpose is not to remove engineering judgment. It is to make the inputs to
that judgment explicit and reviewable.

## 2. Facts, assumptions and unknowns have different authority

The Agent must not flatten all supplied information into a single prompt.

- **Fact**: supported by the current schematic, datasheet, project
  configuration, tool output or hardware observation.
- **Assumption**: a working hypothesis that can guide analysis but cannot
  authorize a destructive action or a completion claim.
- **Unknown**: missing or conflicting information whose consequence must be
  identified.

An unknown does not always stop the whole task. It stops the smallest action
whose safety or validity depends on that information, while independent
inspection can continue.

## 3. Freedom should follow risk

AI assistance can have different levels of freedom inside the same task.

| Work type | Appropriate freedom |
|---|---|
| Read-only tree and dependency analysis | Broad, with source references |
| Host-side script or document update | Bounded by repository scope and tests |
| Firmware implementation | Bounded by hardware facts, architecture and build gate |
| Flash, erase, option-byte or fuse operation | Exact target identification and explicit approval |
| Hardware acceptance claim | Only after current-device evidence |

This prevents convenient automation from silently becoming authority over the
device.

## 4. Prefer deterministic, inspectable behavior

Embedded systems operate under finite memory, timing and recovery constraints.
The Agent should prefer designs whose behavior can be inspected:

- explicit state, events, timeouts and recovery paths;
- bounded buffers, retries and work per iteration;
- clear ownership between application, middleware, drivers and BSP;
- static allocation in deterministic paths unless a bounded alternative is
  justified;
- logs and counters that expose state transitions and failure reasons.

These preferences are defaults, not universal laws. A project may override
them when its resource profile and evidence justify the decision.

## 5. Verification is progressive

Evidence accumulates through distinct gates:

```text
Static inspection
  → host-side checks
  → native generation / build / link
  → artifact identity
  → controlled flash
  → serial or debugger observation
  → physical behavior and recovery
```

Passing one gate does not imply the next. In particular:

`Build Passed ≠ Hardware Validated`

Every result should name the command or observation that supports it. If a gate
cannot run, the Agent reports it as not run or blocked instead of filling the
gap with a plausible explanation.

## 6. Stop conditions are part of the design

A reliable Agent must know when not to continue. Missing target identity,
contradictory pin or voltage information, unavailable toolchains, ambiguous
generated-code ownership and unapproved device mutation are engineering
conditions, not conversational inconveniences.

Stopping the affected stage preserves safety and attribution. The Agent should
still return useful findings, the exact missing fact and the smallest safe next
action.

## 7. Context becomes reusable only after verification

Project context should evolve, but it should not become a collection of stale
AI conclusions. Long-lived constraints are written back only when:

1. their source is known;
2. the relevant build or hardware evidence exists;
3. their scope is tied to a board, SDK or tool version where required;
4. conflicts and superseded facts remain traceable.

This turns completed engineering work into better input for later Agent work
without treating memory as truth by default.

## 8. The intended boundary

These practical constraints help an AI Agent participate in:

- requirement clarification and engineering decomposition;
- project and dependency analysis;
- bounded implementation;
- build and diagnostic tool execution;
- hardware-validation planning and evidence collection;
- verified knowledge maintenance.

The engineer remains responsible for hardware facts, risk decisions, mutation
approval and final acceptance. The objective is a more reliable collaboration
loop, not unattended firmware delivery.

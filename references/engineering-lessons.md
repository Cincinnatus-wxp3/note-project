# Engineering Lessons Behind the Workflow

These lessons are generalized from developer-stated project experience. They guide Agent questions and engineering checks; they do not expose employer/customer implementation or establish current-revision build or hardware results.

## Contents

- [Industrial IoT: protocol abstraction](#1-industrial-iot-protocol-abstraction-is-a-lifecycle-problem)
- [CAT1/LoRa: communication reliability](#2-cat1lora-communication-reliability-requires-explicit-state)
- [Domestic MCU ecosystems](#3-domestic-mcu-ecosystems-sdk-and-tooling-gaps-create-repeated-engineering-work)
- [SDK optimization and Developer Experience](#4-sdk-optimization-developer-experience-is-removal-of-friction)
- [AI Coding and context quality](#5-ai-coding-context-quality-limits-code-quality)
- [Confidentiality boundary](#confidentiality-boundary)

## 1. Industrial IoT: protocol abstraction is a lifecycle problem

### Experience pattern

Industrial gateways and acquisition devices often connect devices with different Modbus variants, register models, reporting intervals and failure behavior.

### General lesson

Protocol abstraction must cover more than encoding and decoding:

- device identity and capability;
- transport ownership;
- timeout, retry and reconnect;
- data validity and timestamp;
- configuration version;
- diagnostic visibility;
- upgrade and compatibility boundaries.

### Agent implication

Before generating a new device adapter, the Agent should identify which difference belongs to transport, protocol, device profile or product behavior. A register table alone is not enough context.

## 2. CAT1/LoRa: communication reliability requires explicit state

### Experience pattern

Cellular and long-range links can be unavailable, delayed or partially functional while the MCU and local acquisition continue running.

### General lesson

Reliable communication requires explicit states and bounded recovery:

- link, session and application delivery are separate states;
- retry must use limits and backoff;
- offline data needs a capacity and discard policy;
- watchdog reset cannot replace root-cause handling;
- logs need enough identity and timing to reconstruct the sequence.

### Agent implication

AI should produce a state/event model and failure policy before adding reconnect code. It must not solve every failure with an infinite loop, long delay or reboot.

## 3. Domestic MCU ecosystems: SDK and tooling gaps create repeated engineering work

### Experience pattern

When MCU SDKs, examples, BootLoader support or tooling are incomplete, the same environment, startup, upgrade and diagnostic work is repeated across projects.

### General lesson

The reusable asset is not only driver code. It includes:

- verified project templates;
- startup/linker conventions;
- BSP and SDK adaptation boundaries;
- BootLoader and partition contracts;
- build/flash/debug commands;
- known failure signatures;
- documentation tied to exact versions.

### Agent implication

The Agent should first identify ecosystem gaps and existing local conventions. It should avoid replacing the build system or inventing a “universal platform” before the current target is understood.

## 4. SDK optimization: Developer Experience is removal of friction

### Experience pattern

Environment installation, repeated project creation, unreadable build errors and error-prone FOTA steps consume development time without adding device capability.

### General lesson

Developer Experience improves when common actions become:

- discoverable;
- deterministic;
- reproducible;
- diagnosable;
- reversible;
- safe by default.

The useful output is a shorter path from intent to evidence, not a larger number of tool features.

### Agent implication

AI should preserve real commands, versions and raw failures, then automate only repeated and well-understood steps. A designed CLI or script is not treated as implemented until it exists and runs.

## 5. AI Coding: context quality limits code quality

### Experience pattern

The practical workflow uses ChatGPT to refine requirements and Codex to inspect and modify the real project. Compilation, serial/J-Link feedback and hardware behavior are returned to the next iteration.

### General lesson

Embedded AI reliability depends on:

`verified context + bounded task + tool execution + build evidence + hardware feedback`

The main hallucination risks are:

- guessed hardware facts;
- wrong SDK/API version;
- ignored generated-code boundaries;
- plausible but unexecuted commands;
- treating build success as device success;
- carrying stale context into a new board or revision.

### Agent implication

The Agent must label facts, assumptions and unknowns; stop at unsafe gaps; run tools; keep evidence; and update long-lived context only after verification.

## Confidentiality boundary

Reusable lessons may include engineering patterns, generic state machines, synthetic protocol examples and public-board validation methods.

They must not include:

- employer or customer source code;
- private register maps or protocols;
- production firmware, logs, endpoints or credentials;
- customer/device identities;
- deployment, sales or other commercial data;
- metrics that cannot be reproduced from saved evidence.

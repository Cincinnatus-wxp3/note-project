# Project Context and Evidence Boundary

Use this file to select relevant questions, safety checks and workflow patterns. Reinspect the current code, branch, hardware, SDK and test environment before turning any historical context into an implementation fact.

## Provenance rule

The background below was stated by the developer and is not, by itself, current-revision evidence. It may guide what the Agent inspects; it must not be used to claim that a module exists, a product shipped, a metric was achieved or a hardware behavior passed.

## Historical pattern sources — not implementation facts

| Developer-stated context source | It may guide the Agent to inspect | It must not establish |
|---|---|---|
| Industrial IoT gateway, CAT1/LoRa DTU, CAT1 water meter and vibrating-wire acquisition work | Link state, protocol abstraction, retries, watchdog/recovery, OTA/BootLoader boundaries, field logs and long-running behavior | Employer/customer code, device maps, product status, deployment scale or current reproducibility |
| ESP32-C3/S3 IoT work | ESP-IDF version, component structure, peripheral ownership, networking, build/flash commands and real-device feedback | Current SDK version, board wiring, enabled components or a successful build |
| STM32G474 Bring-up, BLDC FOC and MPPT exploration | Datasheet/reference-manual checks, timing, sampling, PWM/ADC ownership, safe state and instrument validation | Current board voltage, pinout, protection values, power-stage readiness, FOC operation or loaded testing |
| SDK workflow and Python embedded-tool work | Environment setup, project generation, build-log parsing, firmware/configuration checks and serial/debug evidence | That a named command, tool or efficiency result exists in the current repository |
| Codex and ChatGPT use | Requirement refinement, bounded repository edits, build feedback and hardware evidence loops | That AI output is correct, executed or safe without tool and hardware evidence |

## Current-project inspection rule

For every task, inspect the repository supplied for that task: branch, worktree changes, build instructions, SDK/toolchain, board dependency and available evidence. A project name or historical discussion never proves that a previously described module, transport, test count, timing value or hardware behavior remains implemented or reproducible.

Do not carry counts, sizes, timing values or status from conversation history into a completion report. Re-run the relevant build, tests and hardware checks.

## Developer-confirmed operating pattern

Confirmed tools:

- Codex
- ChatGPT

Confirmed workflow pattern:

1. Describe the hardware, peripherals, relevant schematic constraints, SDK, and project structure.
2. Use ChatGPT to draft and iteratively refine requirements, logic, architecture, algorithms, parameters, failure behavior, and acceptance conditions.
3. Review the requirement and give it with the verified project context to Codex.
4. Let Codex inspect and modify the real project inside explicit path and interface boundaries.
5. Compile, program the device after confirmation, and connect through serial or J-Link.
6. Feed exact errors, logs, debugger state, and hardware behavior back into the next iteration.
7. Store stable conventions as project-local `AGENTS.md` or context documents after verification.

AI does not independently approve electrical safety, protection parameters, hardware facts, or production release.

## Context packet minimum

Before implementation, record:

- Target behavior and non-goals.
- MCU, board revision, voltage domain, clocks, pins, and peripherals.
- SDK, toolchain, build command, flash/debug method, and environment versions.
- Relevant directories, interfaces, generated-code boundaries, and forbidden changes.
- Timing, memory, storage, protocol, and recovery constraints.
- Baseline build/test status.
- Acceptance checks for build, logs, debugger state, and real hardware.

Unknown items remain explicit questions, not AI assumptions.

## Metrics boundary

Do not invent:

- Deployment or production volume.
- User or device count.
- AI efficiency percentages.
- Task-time reductions.
- Test counts, resource use, or timing values that have not been rerun.

Use `[TO BE PROVIDED]` until a repeatable baseline, comparison method, command, and raw result are recorded.

## Confidentiality-safe substitutions

| Restricted material | Safe reusable substitute |
|---|---|
| Company firmware | Minimal self-authored example |
| Customer register table | Synthetic device profile |
| Production log | Generated or redacted log fixture |
| Internal schematic/PCB | Public development board or self-owned minimal board |
| Production endpoint/key | Local mock service and `.env.example` |
| Proprietary OTA package | Documented toy format with generated test image |

Only provide AI with material the developer is authorized to process. Reusable templates must never contain keys, customer identifiers, production endpoints, unique device IDs, or private protocol data.

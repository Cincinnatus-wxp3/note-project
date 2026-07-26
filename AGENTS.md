# AGENTS.md — 个人 AI 辅助嵌入式开发约束规则

## Background / Origin

This file records personal working constraints distilled from real
embedded-development experience. It converts recurring lessons into rules that
an AI Agent can understand, execute, and verify while participating in the
developer's projects.

It is not an industry standard, a complete Agent system, an
open-source framework, a production guarantee, or a claim that firmware
development can be fully autonomous. The rules remain imperative because they
are intended to be applied during actual development, not only read as notes.

The constraints come from recurring patterns in real embedded work:

- Industrial IoT devices exposed multi-device and multi-protocol differences,
  intermittent links, and recovery paths. These patterns led to communication
  layering, explicit state machines, bounded retries, and fault-recovery rules.
- MCU platform adaptation exposed incomplete or version-sensitive SDK support,
  scarce peripheral and memory resources, and board-specific facts. These
  patterns led to Hardware Truth, resource classification, static-design, and
  evidence-source rules.
- SDK and developer-tool improvement exposed repeated environment setup,
  project creation, opaque build failures, and error-prone firmware-update
  steps. These patterns led to explicit workflows, native build gates, tool
  contracts, and evidence-recording rules.
- Bring-up, debugging, and OTA work exposed the gap between plausible code, a
  successful build, and a verified device. These patterns led to approval
  boundaries, hardware-validation gates, and explicit stop conditions.

These origins are generalized and contain no employer code, customer identity,
or commercial data. For explanatory traceability, read
[`docs/origin-of-rules.md`](docs/origin-of-rules.md). When this practical
template is used for a task, the action rules in this file take precedence over
the explanatory material.

## 0. Scope and Working Loop

Use this file as the developer's experience-derived rule set for AI-assisted
embedded work, not as universal platform documentation or tool-specific
configuration. Apply only the rules relevant to the current project, respecting
its IDE, SDK, build system, hardware facts, and local instructions. Use
workflows to select the task sequence; use these rules to constrain analysis,
edits, tool calls, build claims, and hardware claims.

For every firmware task, follow this loop in order:

`Hardware Context → AI Reasoning → Tool Execution → Build → Hardware Validation`

Always record every stage. Execute it, or record why it did not run; use the
exact `BUILD NOT RUN` and `HARDWARE NOT RUN` states for those gates. Do not
describe a stage as complete without evidence. A successful build is not
hardware validation, and plausible code is not a successful build.

Prefer, in this order:

1. Hardware truth over software assumptions.
2. Deterministic behavior over flexibility.
3. Static design over runtime allocation.
4. Explicit state machines over implicit control flow.
5. Minimal architecture over feature richness.

Preserve unrelated user changes. Keep each change bounded by the current task.

## 1. Preflight

Before editing code:

1. Read the task and all repository-local instructions.
2. Inspect the project tree, build files, linker scripts, configuration files,
   generated-code boundaries, and relevant existing tests.
3. Inspect version-control status when available. Never discard unrelated
   changes.
4. Identify the target MCU, board revision, SDK/HAL version, toolchain, build
   system, programmer/debugger, and intended runtime environment.
5. Locate the schematic, pin map, datasheet, reference manual, errata, and
   peripheral examples that the task depends on.
6. Classify the project using section 3.
7. Run the smallest representative baseline build or test when the required
   toolchain is available.
8. Record a task contract before implementation:
   - objective;
   - in-scope files and subsystems;
   - known hardware constraints;
   - unknown or conflicting facts;
   - selected execution model;
   - expected build evidence;
   - expected hardware evidence;
   - explicit non-goals.

If the baseline already fails, preserve the failure output and separate
pre-existing failures from failures introduced by the task.

## 2. Hardware Truth Boundary

Treat the following as hardware facts that require evidence:

- exact MCU part number and board revision;
- supply and I/O voltages;
- oscillator source, clock tree, and peripheral clock;
- pin numbers, alternate functions, pull states, polarity, and electrical
  limits;
- peripheral instances and resource conflicts;
- DMA controller, request, channel, alignment, and accessibility;
- interrupt names, vectors, priorities, and shared IRQ behavior;
- register addresses, bit definitions, reset values, and errata constraints;
- Flash/RAM sizes, linker regions, boot addresses, sectors, and OTA layout;
- external-device addresses, timing, reset sequencing, and protocol limits;
- debugger/programmer wiring and target interface voltage.

Use evidence in this order:

1. Project schematic, board files, and verified net or pin tables.
2. Exact-device datasheet, reference manual, programming manual, and errata.
3. Board and external-device documentation.
4. SDK/HAL headers, linker scripts, generated configuration, and vendor
   examples for the exact version in use.
5. Existing project code and recorded hardware observations.

Never invent a missing pin, clock, voltage, register, DMA mapping, memory
address, peripheral capability, or board connection.

When a required hardware fact is missing or conflicting:

1. Stop the affected implementation or hardware action.
2. State exactly which fact is missing.
3. State which artifact can resolve it.
4. Continue only with work that does not depend on that fact.

You may infer software architecture defaults only when they stay within the
known specification and do not imply an unknown hardware fact. Label every
such inference as an assumption and keep it reversible.

## 3. Resource Class and System Profile

Classify MCU firmware on two independent axes before selecting its
architecture.

### Resource class

Use the ranges below as personal working heuristics, not as a universal MCU
classification. Project-specific resource, timing, SDK, and workload evidence
always takes precedence.

- **Class A — Ultra-constrained:** RAM below 64 KiB; default to bare metal,
  static memory, and minimal scheduling. Require measured resource and timing
  evidence before introducing an RTOS.
- **Class B — Standard embedded/IoT:** RAM from 64 KiB up to but not including
  256 KiB; normally use DMA plus explicit FSMs where the workload requires
  them.
- **Class C — Full embedded/IoT:** RAM of at least 256 KiB; an RTOS is allowed
  but not automatically required.
- **N/A:** the task has no MCU target, such as a host-only developer tool or
  documentation/context-maintenance task.

### System profile

- **Profile D — Industrial hybrid:** multiple sensors plus networking and a
  control loop; isolate timing-critical paths from communication and
  maintenance paths.
- **Not D:** the verified workload does not have that combination.
- **N/A:** the task has no firmware system scope.

Base the resource class on verified memory and the system profile on verified
workload. Profile D is orthogonal to Class A/B/C and never overrides actual
resource limits. If evidence is insufficient, record `Unknown` and choose only
decisions valid across the unresolved possibilities.

## 4. Architecture and Execution-Model Selection

For new or affected code, prefer clear downward dependencies when they fit the
existing project:

`Application → Middleware → Driver → BSP → Hardware`

Do not restructure a working project solely to match this model. Use these
boundaries as practical review guidance:

- Application code depends on service interfaces, not registers or HAL
  internals.
- Middleware depends on driver interfaces, not board pin definitions.
- Drivers own peripheral behavior and expose bounded interfaces.
- BSP owns board-specific pin, clock, reset, and peripheral binding.
- Hardware definitions come from verified device and board sources.
- Lower layers never call application logic directly; report events through
  explicit callbacks, queues, flags, or interfaces.

Select the smallest execution model that satisfies verified concurrency and
timing needs:

- **Super loop:** ordered, low-concurrency work with bounded execution time.
- **Event FSM:** asynchronous protocol or device state transitions.
- **DMA pipeline:** continuous or high-rate data movement.
- **Cooperative scheduler:** several bounded periodic jobs without preemption.
- **RTOS:** only when concurrency, networking, blocking isolation, or timing
  requirements justify tasks and synchronization.

Do not add an RTOS to compensate for unclear state ownership or blocking
drivers. Record why the selected model is sufficient and what condition would
force a different model.

## 5. Required Subsystem Rules

### 5.1 Memory

- Do not allocate from the heap in an ISR, deterministic control loop, or
  high-frequency path.
- Prefer compile-time-sized static storage for bounded firmware-owned data.
- Framework-owned or initialization-time allocation is allowed only when the
  existing platform requires it, failure is handled, and the memory budget is
  measured.
- Do not introduce unbounded allocation or allocation into a previously
  deterministic path without an explicit requirement and evidence.
- Make DMA buffers static, correctly aligned, and placed in DMA-accessible
  memory.
- Define buffer ownership, capacity, lifetime, and overflow behavior.
- Validate every length before copy, parse, or transfer.
- Check stack, Flash, RAM, section, and linker-map impact after relevant
  changes.
- Do not hide large buffers in local scope or duplicate buffers without a
  measured reason.

### 5.2 Interrupts and Concurrency

- Keep ISRs minimal: capture status, move bounded data, clear the verified
  source, and signal deferred work.
- Never block, allocate memory, parse complete protocols, log synchronously, or
  wait for locks inside an ISR.
- Mark shared state correctly and protect multi-context access with the
  mechanism appropriate to the selected execution model.
- Define ownership for every shared buffer, queue, flag, and peripheral.
- Verify interrupt priority and RTOS-call restrictions before using an API
  from an ISR.

### 5.3 Communication and Streaming Protocols

Separate the communication stack into transport, framing, protocol, service,
and application concerns. Do not let application logic own link recovery,
byte-stream parsing, or modem/device state.

Assign every UART one explicit role:

`DEBUG | AT | SENSOR | PROTOCOL | BRIDGE`

Use this receive path unless hardware or protocol evidence requires a
documented deviation:

`DMA/ISR → Ring Buffer → FSM → Decoder → Application`

Define and test:

- partial frames and back-to-back frames;
- corrupted length, checksum, escape, and delimiter fields;
- ring-buffer overflow and resynchronization;
- inter-byte and whole-frame timeout;
- disconnect, reconnect, and peripheral reset;
- binary data containing delimiter-like bytes;
- ownership when TX and RX operate concurrently.

For every external link, also define:

- connection and session lifecycle;
- request/response correlation and timeout ownership;
- retry limits, backoff, idempotency, and duplicate handling;
- offline buffering and overflow policy when applicable;
- reconnect, resubscribe, resynchronization, and degraded-mode behavior;
- observability sufficient to distinguish transport, protocol, remote-service,
  and application failures.

Do not parse an unbounded frame in an ISR. Do not assume a complete frame
arrives in one DMA or UART callback.

### 5.4 State Machines

Represent lifecycle behavior explicitly:

`INIT → IDLE → RUNNING → ERROR → RECOVERY`

For every FSM:

- enumerate states and events;
- define entry and exit actions;
- define transition guards and timeouts;
- define invalid-event behavior;
- bound retries;
- define how success, failure, and recovery are observed;
- keep transitions testable without real time where practical.

Do not encode persistent protocol or device state as scattered booleans.

### 5.5 Defensive Behavior and Fault Tolerance

- Validate all external input and public API parameters.
- Return explicit statuses such as `OK`, `ERROR`, `TIMEOUT`,
  `INVALID_PARAM`, and `BUSY`.
- Do not fail silently.
- Apply bounded retry, timeout, reset, and recovery where the fault model
  requires them.
- Prevent retry storms and infinite recovery loops.
- Preserve the first actionable error and enough context to diagnose it.
- Put the system into a defined safe or degraded state when recovery fails.

### 5.6 Initialization

Use this default order:

`BSP → Clock → Interrupt → Driver → Middleware → Application`

Change the order only when startup code or verified hardware requirements
require it. Document the dependency that justifies the deviation. Do not
enable an interrupt before its state, buffer, handler, and peripheral are
ready.

## 6. AI Reasoning Checklist

Before implementation, convert the task contract into a bounded change plan:

1. Map each requirement to an existing layer, module, or new interface.
2. Separate verified facts, software assumptions, and unresolved questions.
3. Identify timing, memory, concurrency, protocol, and fault implications.
4. Select acceptance checks for host logic, build output, and hardware
   behavior.
5. List the files expected to change and avoid speculative refactors.

Reason from repository evidence before generating code. Reuse existing project
patterns when they are correct for the verified target. Do not copy an API,
register sequence, or example from a different MCU/SDK version without
checking compatibility.

## 7. Tool Execution Rules

Use tools to close the reasoning loop:

1. Search before editing. Trace definitions, call sites, configuration, and
   generated-code ownership.
2. Read the smallest complete context needed to preserve behavior.
3. Make one logically bounded change at a time.
4. Run the narrowest relevant formatter, generator, static check, or host test.
5. Build with the project's native command and exact target configuration.
6. Use compiler and linker output as evidence; fix the first causal error
   instead of masking downstream errors.
7. Inspect the map, size, binary metadata, or generated configuration when the
   change affects memory layout, boot, or peripheral binding.
8. Repeat reasoning and execution only from new evidence.

An implementation request authorizes only in-scope repository edits. Obtain
explicit human approval before any device mutation, including erase, flash,
OTA, reset with state loss, target halt, memory/register write, option-byte or
fuse change, calibration write, or boot-region change. State the exact target,
artifact, mutation, risk, and recovery path before requesting approval.

Do not:

- rewrite vendor or generated code when an extension point exists;
- suppress warnings or disable checks merely to obtain a green build;
- make unrelated formatting or architecture changes;
- claim a command ran when it did not;
- substitute a mock result for hardware validation;
- flash, erase, unlock, change option bytes, or update boot regions without a
  verified target and explicit human approval.

## 8. Build Gate

A build gate passes only when:

- the exact target and configuration are identified;
- generation/configuration steps complete when required;
- compilation and linking complete successfully;
- no new unexplained warnings are introduced;
- binary size and section placement remain within verified limits;
- relevant host/unit/static tests pass or their absence is reported.

Record:

- exact command;
- toolchain and SDK version when available;
- target/configuration;
- exit result;
- warnings;
- Flash/RAM or section impact when relevant;
- artifact name and identity.

Report exactly one build state:

- `BUILD PASSED` — the required build and checks ran and passed.
- `BUILD FAILED` — a required build or check ran and failed.
- `BUILD NOT RUN` — the build was not applicable or could not run; record why.

If build tools are unavailable, report `BUILD NOT RUN`; do not report
`BUILD PASSED`.

## 9. Hardware Validation Gate

Before connecting, powering, flashing, or debugging:

1. Confirm the exact board/MCU and target interface voltage.
2. Confirm debugger/programmer wiring and reset/boot state.
3. Confirm the artifact belongs to that target and configuration.
4. Confirm the requested action does not erase calibration, credentials,
   option bytes, bootloader, or unrelated Flash.
5. Obtain explicit human approval for the exact state-changing action. An
   approved source-code change is not approval to mutate a device.

Then use the available real tools as appropriate:

- programmer or bootloader for flashing;
- serial terminal or capture tool for logs and protocol evidence;
- J-Link, GDB, or OpenOCD for halt state, registers, memory, and breakpoints;
- oscilloscope, logic analyzer, meter, or external fixture when software logs
  cannot prove electrical or timing behavior.

Validate observable acceptance criteria, including startup, normal operation,
timeouts, error paths, recovery, and repeated operation where relevant.
Preserve firmware identity, command output, logs, and measured observations.

Use exactly one hardware status in the completion report:

- `HARDWARE PASSED` — acceptance criteria ran on the named hardware and passed.
- `HARDWARE FAILED` — validation ran and the observed failure is recorded.
- `HARDWARE NOT RUN` — hardware, access, facts, or tools were unavailable.

Never convert simulation, compilation, static review, or expected behavior
into `HARDWARE PASSED`.

## 10. Stop Conditions

Stop the affected action and report the blocker when:

- a required hardware fact is missing or conflicts across sources;
- the exact target, board revision, voltage, or binary cannot be confirmed;
- a command would erase, unlock, overwrite, or reconfigure data outside the
  explicit task;
- the required build, flash, debug, or measurement tool is unavailable;
- the baseline failure prevents attribution of the new result;
- the requested behavior violates verified electrical, timing, memory, or
  safety limits;
- success cannot be observed with the available validation path.

Do not use a stop condition to abandon safe, independent inspection or host
validation. State what was completed, what remains blocked, and the minimum
evidence or access needed to continue.

## 11. Completion Report Template

End every task iteration, including a blocked iteration, with a concise
evidence report containing:

1. **Acceptance results:** list each criterion as `Verified`,
   `Implemented, not rerun`, `Planned`, `Illustrative`, or `Blocked`.
2. **Classification:** resource class A/B/C, `Unknown`, or `N/A`; and system
   profile D, Not D, `Unknown`, or `N/A`, with evidence.
3. **MCU/board:** exact verified identity, `Unknown`, or `N/A` for a task with
   no MCU/board scope.
4. **Hardware context:** sources used, facts relied on, and unresolved facts.
5. **Execution model:** selected model and reason.
6. **Changes:** files and behavior changed.
7. **Tool execution:** important searches, generators, tests, and diagnostics.
8. **Build:** exact command and `BUILD PASSED`, `BUILD FAILED`, or
   `BUILD NOT RUN`.
9. **Hardware validation:** named board and `HARDWARE PASSED`,
   `HARDWARE FAILED`, or `HARDWARE NOT RUN`, plus evidence.
10. **Applicable subsystem details:** for every affected UART, memory/DMA
    plan, FSM, or layer dependency, report the relevant map and behavior. Use
    one concise `N/A` entry when none of these subsystems is affected.
11. **Assumptions and remaining risks:** only explicit, reversible software
    assumptions; never invented hardware facts.

## 12. Forbidden Shortcuts

Never:

- invent hardware context;
- overuse an RTOS;
- introduce unbounded or unmeasured heap allocation into deterministic runtime
  paths;
- block or perform full protocol work in an ISR;
- couple application code directly to BSP, HAL, or registers;
- hide persistent state in unrelated globals;
- omit timeout and recovery behavior from an external interaction;
- treat generated code, a successful build, or an AI explanation as proof of
  real hardware behavior.

The final authority is always verified hardware behavior within the specified
electrical, timing, memory, and safety constraints.

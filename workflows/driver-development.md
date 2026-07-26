# Driver Development Workflow

This is a personal workflow summary distilled from real embedded development,
not a universal engineering process. Adapt it to the current project's verified
facts, tools, and risk boundaries.

Personal baseline: `Requirement → Analysis → Implementation → Validation`.

Use this workflow to add or adapt an MCU peripheral driver, external-device driver, bus transport or SDK/HAL integration.

`Input → Datasheet/Project Analysis → Driver Contract → Implementation → Build/Hardware Validation → Reusable Context`

## 1. Input

Required inputs:

- exact MCU part number, package and board revision;
- SDK/HAL version and toolchain;
- relevant schematic page and verified pin/bus ownership;
- exact peripheral instance and clock source;
- exact external-device part number and document revision;
- register map, timing, reset and electrical constraints;
- interrupt/DMA mapping when used;
- existing BSP, driver interfaces and initialization order;
- expected data, error and recovery behavior;
- validation equipment and evidence root.

Do not reuse a register sequence, SDK call or vendor example from another device/version without compatibility evidence.

## 2. AI Analysis

Require Codex to produce:

- hardware facts and their sources;
- unresolved or conflicting facts;
- existing driver/BSP patterns;
- resource conflicts;
- proposed API and ownership model;
- initialization and shutdown order;
- interrupt/DMA/threading model;
- timeout, error mapping and recovery path;
- minimal staged validation plan.

Create a driver contract:

```yaml
driver:
  owns: []
  depends_on: []
  public_api: []
  buffers:
    ownership: ""
    capacity: ""
    overflow: ""
  execution:
    context: "ISR/task/super-loop"
    blocking: false
  errors: []
  recovery: []
  hardware_acceptance: []
```

## 3. Engineering Change

Implement by layer:

1. keep board pins/clocks/resets in BSP;
2. keep peripheral behavior in the driver;
3. expose bounded interfaces to middleware/application;
4. keep ISR work minimal and defer parsing or policy;
5. define buffer ownership and bounds;
6. validate lengths, addresses and state;
7. make timeouts and retry limits explicit;
8. preserve generated-code extension points;
9. add host-testable parsing/state logic where practical.

For streaming UART/SPI/I2C data, define partial-frame, timeout, corruption, overflow and resynchronization behavior.

## 4. Tool and Build Validation

Validate incrementally:

- compile the smallest affected target;
- run parser/FSM/fixture tests;
- inspect generated configuration;
- run the project’s native build;
- inspect warnings, symbols, sections and memory impact;
- confirm the artifact matches the intended target.

Report `BUILD PASSED`, `BUILD FAILED` or `BUILD NOT RUN`.

## 5. Hardware Validation

After explicit approval:

1. confirm target, voltage and wiring;
2. validate power/reset/identity before functional traffic;
3. use a known input or known device response;
4. capture raw bus/log/debug evidence;
5. verify normal transfer, timeout and recovery;
6. verify repeated operation and safe failure behavior;
7. compare measurements with documented tolerances.

Report `HARDWARE PASSED`, `HARDWARE FAILED` or `HARDWARE NOT RUN`.

Do not mark the driver `Verified` from compilation or a simulated response alone.

## 6. Result and Context Preservation

Preserve:

- document revisions and verified mapping;
- driver contract and affected dependency path;
- build artifact and memory impact;
- raw hardware observations;
- unsupported modes and remaining risks;
- reusable init, diagnostic and recovery rules.

Invalidate the context when board revision, MCU, SDK/HAL or device revision changes.

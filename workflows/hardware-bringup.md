# Hardware Bring-up Workflow

This is a personal workflow summary distilled from real board Bring-up work,
not a universal engineering process. Adapt it to the current project's verified
facts, tools, and safety boundaries.

Personal baseline: `Requirement → Analysis → Staged Implementation → Validation`.

Use this workflow for a new MCU board, board revision, clock/debug path or first low-level peripheral validation.

Default to the lowest-risk observable state.

`Input → Safety/Document Analysis → Staged Bring-up → Build/Program → Hardware Gates → Context Preservation`

## 1. Input

Required before power or programming:

- schematic and board revision;
- exact MCU part number and package;
- expected rails, current limit and power sequence;
- reset, boot, clock and SWD/JTAG mapping;
- verified pin and alternate-function mapping;
- safe state and prohibited outputs;
- SDK/toolchain, startup, linker and target configuration;
- probe/programmer and measurement equipment;
- explicit stop conditions;
- evidence root.

Missing electrical or board facts block the affected stage.

When the available evidence supports only low-voltage logic validation, keep the scope at logic level and no load unless the developer supplies and approves a different safety plan. A safety scope limit is not an assertion about the current board.

## 2. AI Analysis

Cross-check schematic, datasheet, reference manual, programming manual, errata, package pinout and exact SDK/HAL version.

Require Codex to output:

- confirmed facts with source;
- conflicting and missing facts;
- resource class, system profile and execution model;
- startup/linker/clock dependency;
- pin and peripheral ownership;
- initialization order;
- safe-state and fault path;
- one minimal observable result per stage;
- tool sequence, approval points and stop conditions.

AI may propose a reversible software structure. It may not invent wiring, voltages, alternate functions, protection parameters or measurements.

## 3. Engineering Preparation

Before enabling application outputs:

1. inspect startup, linker, clock and board files;
2. establish an unmodified build baseline;
3. force active outputs to their verified safe state;
4. isolate Bring-up observations from application behavior;
5. prepare one change and one observation per gate;
6. define how to halt and recover safely.

## 4. Build and Program

Record:

- toolchain and SDK versions;
- exact clean build command and target;
- ELF/HEX/BIN/MAP identity;
- current Flash/RAM output;
- startup/linker files;
- warnings and unresolved symbols.

Report `BUILD PASSED`, `BUILD FAILED` or `BUILD NOT RUN`.

Before program/erase/reset:

- identify the exact target and interface voltage;
- identify artifact, address/partition and source revision;
- state persistent effects and recovery path;
- obtain explicit developer approval.

## 5. Hardware Gates

Do not advance while the previous gate is unresolved.

### Gate A — Power and debug

- rails/current inside the approved range;
- reset/boot behavior observed;
- probe connection stable;
- device identity matches.

### Gate B — Clock and time base

- source/configuration matches documents;
- a safe output or debugger observation confirms the expected time base.

### Gate C — Communication

- exact serial/debug parameters recorded;
- raw output captured;
- no unexplained reset or fault.

### Gate D — Peripheral

- validate one ADC/PWM/UART/I2C/SPI/GPIO path at a time;
- use a known stimulus;
- compare with a numeric or behavioral criterion.

### Gate E — Fault and safe state

- inject only approved low-risk faults;
- confirm bounded recovery or safe-state transition;
- save logs, registers or waveform evidence.

Report `HARDWARE PASSED`, `HARDWARE FAILED` or `HARDWARE NOT RUN`.

Stop immediately on unexpected current/voltage, target mismatch, unsafe output, unexplained programming failure or missing measurement capability.

## 6. Result and Context Preservation

For each gate, save:

- target and board identity;
- build artifact and tool output;
- exact wiring/instrument setup;
- expected and actual observation;
- acceptance status and next gate;
- known limitations.

Only a gate with current build traceability and direct hardware observation can be `Verified`.

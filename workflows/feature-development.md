# Feature Development Workflow

This is a personal workflow summary distilled from real embedded development,
not a universal engineering process. Adapt it to the current project's verified
facts, tools, and risk boundaries.

Personal baseline: `Requirement → Analysis → Implementation → Validation`.

Use this workflow to add one observable firmware or embedded developer-tool behavior to an existing project.

The process is:

`Input → AI Analysis → Engineering Change → Tool/Build/Hardware Validation → Context Preservation`

## 1. Input

Collect before implementation:

- problem or requirement background;
- observable target behavior and explicit non-goals;
- MCU, board revision, voltage domain, clocks, pins and peripherals;
- SDK/toolchain versions, project root, source revision and build command;
- existing module boundaries and generated-code ownership;
- protocol, timing, memory, recovery and compatibility constraints;
- allowed and forbidden paths;
- acceptance criteria and available validation equipment;
- project-selected evidence root and task identifier.

Mark unknown hardware or SDK facts as `Unknown`. If an unknown can change pins, registers, protocol interpretation, memory layout, timing or safety, set the affected criterion to `Blocked`.

## 2. AI Analysis

### Requirement reasoning

Use ChatGPT, when needed, to organize:

- user-visible or device-visible behavior;
- state transitions, inputs, outputs and timing;
- failure, timeout, retry and recovery behavior;
- architecture and interface changes;
- parameters and their evidence source;
- build and hardware acceptance criteria.

Review the result manually. ChatGPT output is a draft, not an engineering fact.

### Project reasoning

Require Codex to inspect the real project and output:

1. affected call path and dependency layers;
2. existing patterns that should be reused;
3. generated/vendor code boundaries;
4. resource, timing and concurrency risks;
5. smallest verifiable implementation slice;
6. exact files expected to change;
7. validation ladder and stop conditions.

Create a task contract:

```yaml
task:
  status: Planned
  goal: "one observable behavior"
  non_goals: []
  allowed_paths: []
  forbidden_changes: []
  interfaces_to_preserve: []
  confirmed_facts: []
  unresolved_facts: []
  acceptance:
    source: []
    tests: []
    build: []
    hardware: []
```

## 3. Engineering Change

Implement one bounded slice:

1. establish the unmodified baseline;
2. edit only approved paths;
3. preserve existing public interfaces unless the contract changes them;
4. keep hardware binding in BSP/driver layers;
5. express persistent behavior with explicit states and transitions;
6. define timeout, error and recovery behavior;
7. avoid unrelated refactors and speculative abstractions;
8. review the diff before validation.

Do not implement from an unverified register value, pin, clock, address or SDK API.

## 4. Tool and Build Validation

Run the project’s actual tools in order:

1. formatter/static checks when configured;
2. focused host tests or fixtures;
3. generated-code checks when applicable;
4. native project build;
5. artifact and map/size inspection when relevant.

Record exact commands, versions, working directories, exit codes and raw output paths. Report exactly one Build state:

- `BUILD PASSED`
- `BUILD FAILED`
- `BUILD NOT RUN`

A failed build blocks device programming.

## 5. Hardware Validation

After explicit approval for the exact device mutation:

1. confirm target board and artifact identity;
2. program/verify/reset using the project’s real tool;
3. capture raw serial and debugger output;
4. exercise the target behavior;
5. exercise safe error and recovery paths;
6. compare every observation with an acceptance criterion.

Report:

- `HARDWARE PASSED`
- `HARDWARE FAILED`
- `HARDWARE NOT RUN`

Build success never substitutes for hardware validation.

## 6. Result and Context Preservation

Save:

- final task contract and changed paths;
- build command, result and artifact identity;
- hardware target, operation approval and raw evidence;
- acceptance-criterion statuses;
- remaining risks and smallest safe next action.

Only move verified, reusable facts into project-local Agent context. Keep one-off debugging history in the evidence record.

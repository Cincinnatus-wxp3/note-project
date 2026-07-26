# Embedded Tool Contracts

Use these contracts whenever Codex invokes or asks the developer to invoke a build, flash, serial, debugger, configuration, or hardware-validation tool.

Store real outputs according to [evidence-record.md](evidence-record.md). The project must choose an evidence root before the first mutating command.

## Contents

- [Common execution record](#common-execution-record)
- [Read and inspection](#read-and-inspection)
- [Build contract](#build-contract)
- [Flash/program contract](#flashprogram-contract)
- [Serial contract](#serial-contract)
- [J-Link / GDB / OpenOCD contract](#j-link--gdb--openocd-contract)
- [Python developer-tool contract](#python-developer-tool-contract)
- [Hardware observation contract](#hardware-observation-contract)
- [Failure feedback packet](#failure-feedback-packet)

## Common execution record

Record every meaningful call in this shape:

```yaml
tool_call:
  purpose: "[TO BE PROVIDED]"
  tool: "[TO BE PROVIDED]"
  version: "[TO BE PROVIDED]"
  working_directory: "[TO BE PROVIDED]"
  command: "[TO BE PROVIDED]"
  inputs:
    source_revision: "[TO BE PROVIDED]"
    artifact: "[TO BE PROVIDED]"
    target: "[TO BE PROVIDED]"
  mutates_state: false
  approval: "not_required"
  result:
    exit_code: "[TO BE PROVIDED]"
    summary: "[TO BE PROVIDED]"
    evidence_path: "[TO BE PROVIDED]"
```

Do not write a successful result before the tool has run.

## Read and inspection

Allowed without device mutation:

- List/search project files.
- Read code and project documentation.
- Inspect build files and configuration.
- Inspect an existing artifact.
- Read version information.
- Read serial/debugger state when the operation does not change target state.

Output:

- Files or modules inspected.
- Relevant current behavior.
- Unknowns and conflicts.

## Build contract

Before:

- Record source revision and uncommitted changes.
- Record toolchain/SDK.
- Record the baseline result when available.

Execute the project’s existing build command. Do not invent or replace the build system for convenience.

After:

- Record exit code.
- Save the minimal relevant output plus the raw log path.
- Identify new versus baseline warnings.
- Record produced artifacts.
- Record map/size only from current output.

Gate:

- A failed build blocks flash and hardware validation.
- An unexplained critical warning blocks completion.
- Record exactly one state: `BUILD PASSED`, `BUILD FAILED`, or `BUILD NOT RUN`.

## Flash/program contract

Treat erase, flash, OTA/FOTA, configuration installation, Option Bytes and fuse changes as state-changing operations.

Before:

1. Identify the exact target.
2. Identify the exact artifact and source revision.
3. Confirm power/debug connection and safe state.
4. State the operation risk and the recovery or rollback path. If recovery is
   not available, state that explicitly and do not imply reversibility.
5. Show the exact command and expected mutation.
6. Obtain explicit developer confirmation for that target, artifact and
   mutation.

After:

- Save program/verify/reset output.
- Do not claim success if verify did not run or did not pass.
- Record the firmware/configuration identity observed on the device.

## Serial contract

Record:

- Port identity without exposing sensitive machine data outside its boundary.
- Baud rate, parity, stop bits and flow control.
- Capture start/end time.
- Firmware/configuration identity.
- Raw log path.

Preserve raw data. Parsed events and AI summaries must point back to the raw range.

Do not equate “no output” with one specific cause; distinguish connection, parameter, reset, power and firmware possibilities.

## J-Link / GDB / OpenOCD contract

Record:

- Probe and tool version.
- Target configuration.
- Connection command.
- Halt/reset state.
- Program counter, fault registers, backtrace or relevant register values.

Separate observations from interpretation.

Before changing memory, registers, option bytes or execution state beyond ordinary debug control, obtain confirmation when the action can affect the device persistently or create risk.

## Python developer-tool contract

For serial, log, firmware, Profile or diagnostic tools:

- Run the real module/entry point found in the current source.
- Record dependencies and input fixture.
- Preserve original input.
- Distinguish parse failure, unsupported format and missing data.
- Make every derived conclusion traceable to an input field or line.
- Do not invent a command shown only in a design document.

## Hardware observation contract

Record:

- Board and connection.
- Instrument and relevant settings.
- Test point or signal.
- Input condition.
- Expected condition and tolerance.
- Actual observation.
- Capture path.

AI may compare evidence with acceptance criteria. It may not invent a measurement or independently approve electrical safety.

## Failure feedback packet

```yaml
feedback:
  stage: "[TO BE PROVIDED]"
  command_or_action: "[TO BE PROVIDED]"
  expected: "[TO BE PROVIDED]"
  actual: "[TO BE PROVIDED]"
  raw_evidence: "[TO BE PROVIDED]"
  confirmed_context: []
  unresolved_context: []
  mutation_performed: false
  next_safe_observation: "[TO BE PROVIDED]"
```

Feed this packet into the next AI reasoning step. Keep the next code change separate from the evidence collection step.

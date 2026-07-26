# Bug Analysis Workflow

This is a personal workflow summary distilled from real embedded debugging,
not a universal engineering process. Adapt it to the current project's verified
facts, tools, and risk boundaries.

Personal baseline: `Problem → Analysis → Change → Validation`.

Use this workflow for build failures, resets, communication faults, invalid sensor values, timing problems, OTA/FOTA failures or field symptoms.

Do not begin with a code change. Begin with an observable symptom and evidence.

`Input → AI Analysis → Evidence-Guided Change → Comparative Validation → Knowledge Preservation`

## 1. Input

Record:

- source revision, worktree state and firmware identity;
- exact board, connected hardware and configuration;
- SDK/toolchain and build target;
- expected behavior and observed symptom;
- first known occurrence and reproduction steps;
- raw build, serial, debugger or measurement evidence;
- available tools, device access and safety boundary;
- evidence root and confidentiality boundary.

If the evidence comes from a private environment, preserve the authorized original before creating a redacted copy.

## 2. AI Analysis

Give Codex or ChatGPT the exact evidence, not only a natural-language summary.

Require the analysis to separate:

1. confirmed facts;
2. unresolved context;
3. candidate causes;
4. evidence that distinguishes the candidates;
5. lowest-risk next observation;
6. a bounded change only after evidence supports it.

Use a hypothesis table:

| Candidate cause | Supporting evidence | Contradicting evidence | Next discriminating check |
| --- | --- | --- | --- |
| `[candidate]` | `[actual evidence]` | `[actual evidence or none]` | `[one safe observation]` |

Do not accept a root cause because it is common or plausible. Check power, wiring, transceiver, protocol version and configuration before assuming a software defect.

## 3. Evidence Collection

Prefer observation before mutation:

1. reproduce on the recorded baseline;
2. capture raw logs with firmware identity;
3. inspect reset reason, fault registers, backtrace or task state;
4. inspect build configuration, generated files and map output;
5. capture bus traffic or waveform when logs cannot distinguish causes;
6. run a focused fixture or host test.

Preserve command, tool version, parameters, exit result and raw evidence location.

If the symptom cannot be reproduced and no original evidence remains, report `Blocked` rather than inventing a cause.

## 4. Engineering Change

When evidence supports a cause:

- change one causal mechanism at a time;
- preserve a control/baseline;
- keep the diff inside the task contract;
- add a focused regression check when practical;
- define expected recovery and negative behavior;
- avoid masking the symptom with a broad retry, delay or warning suppression.

Record why the selected change is supported by evidence.

## 5. Build and Comparative Validation

Run focused checks and a clean build. Report:

- `BUILD PASSED`
- `BUILD FAILED`
- `BUILD NOT RUN`

After explicit device-mutation approval:

1. program the traceable artifact;
2. repeat the original reproduction steps;
3. capture the same evidence channels used for the baseline;
4. compare before and after;
5. check a safe negative/recovery case;
6. check for unrelated regression.

Report:

- `HARDWARE PASSED`
- `HARDWARE FAILED`
- `HARDWARE NOT RUN`

A hardware-dependent fix is `Verified` only when the original symptom no longer reproduces under equivalent conditions and the new evidence supports the stated cause.

## 6. Result and Knowledge Preservation

Save an incident record:

```yaml
incident:
  symptom: "observed behavior"
  environment: "board/firmware/sdk/toolchain"
  reproduction: []
  evidence: []
  selected_cause:
    status: "confirmed or unresolved"
    basis: []
  change_scope: []
  build_state: "BUILD PASSED | BUILD FAILED | BUILD NOT RUN"
  hardware_state: "HARDWARE PASSED | HARDWARE FAILED | HARDWARE NOT RUN"
  remaining_risks: []
```

Promote only repeatable failure signatures, diagnostic commands and validated recovery rules into long-lived context.

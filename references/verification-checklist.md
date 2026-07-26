# Embedded task verification checklist

Run this checklist before accepting an AI-assisted change, preserving it as reusable context, or sharing a reproducible example.

## Scope and context

- [ ] The target behavior, non-goals, and acceptance conditions are explicit.
- [ ] MCU, board revision, SDK, toolchain, build entry, and relevant peripherals are recorded.
- [ ] Allowed paths, forbidden paths, generated-code boundaries, and interfaces are explicit.
- [ ] Timing, memory, storage, protocol, electrical, and safety constraints are recorded.
- [ ] Unknown hardware or SDK facts remain marked as unknown.
- [ ] A project-local evidence root and task identifier were selected.

## Baseline

- [ ] Existing user changes were inspected and preserved.
- [ ] The baseline build command and result were recorded.
- [ ] Relevant existing tests were run or their absence was recorded.
- [ ] Pre-existing warnings and failures are separated from failures introduced by this task.

## AI-assisted implementation

- [ ] ChatGPT output was reviewed before becoming a requirement or design input.
- [ ] Codex inspected the real project before editing.
- [ ] The change stays inside the task contract.
- [ ] Unrelated refactors and speculative API changes were excluded.
- [ ] AI assumptions were checked against code, official documentation, or hardware evidence.
- [ ] Human decisions about safety, pins, clocks, protection, erase, flash, and OTA are visible.

## Build and tests

- [ ] Formatting, lint, or static checks were run when available.
- [ ] The current revision builds with the recorded command.
- [ ] Relevant host or target tests pass, or each failure is explained.
- [ ] Exact counts, sizes, timing, and warnings come from saved output.
- [ ] The artifact programmed to the device is traceable to the recorded build.
- [ ] Build is reported as exactly `BUILD PASSED`, `BUILD FAILED`, or `BUILD NOT RUN`.

## Flash and device feedback

- [ ] State-changing operations were explicitly confirmed before execution.
- [ ] Flash/program/verify/reset output was saved.
- [ ] Serial parameters and raw log evidence were saved.
- [ ] J-Link, GDB/OpenOCD, registers, or fault state were captured when relevant.
- [ ] Real peripheral or board behavior was compared with the acceptance conditions.
- [ ] If hardware was unavailable, the result clearly says “hardware validation not performed.”
- [ ] Hardware is reported as exactly `HARDWARE PASSED`, `HARDWARE FAILED`, or `HARDWARE NOT RUN`.

## Failure loop

- [ ] The exact failing command, error, log, or hardware behavior was fed into the next analysis step.
- [ ] Each fix has new evidence rather than relying on the previous explanation.
- [ ] Remaining blockers, hypotheses, and manual follow-up steps are explicit.

## Reusability

- [ ] Stable conventions were separated from one-off project details.
- [ ] Project-local context matches the current SDK, board, and source revision.
- [ ] Reusable instructions contain no credentials or uncertain guesses.
- [ ] Architecture diagrams, directory trees, commands, and configuration examples match the current project.
- [ ] Efficiency metrics have a repeatable baseline and raw record.

## Confidentiality

- [ ] AI received only material the developer is authorized to process.
- [ ] No private protocol map, register table, board file, production firmware, or production log was copied into reusable examples.
- [ ] No keys, certificates, tokens, endpoints, unique IDs, personal data, or location data are present.
- [ ] Shareable examples use self-authored code, public boards, synthetic data, or redacted fixtures.
- [ ] Repository history and generated artifacts were checked when material leaves the project boundary.

## Truth and status

- [ ] Each acceptance criterion is labeled `Verified`, `Implemented, not rerun`, `Planned`, `Illustrative`, or `Blocked`.
- [ ] A hardware-dependent criterion is `Verified` only when its direct evidence is saved and the task has `HARDWARE PASSED`.
- [ ] Compilation is not described as hardware verification.
- [ ] Experience-derived patterns are not treated as current project facts without fresh inspection.
- [ ] Bring-up scope stays within the verified electrical and load boundary.
- [ ] Only Codex and ChatGPT are named as AI tools.

## Done gate

A task is complete only when:

1. The current revision has recorded build and relevant test results.
2. Every hardware-dependent acceptance criterion has direct evidence and `HARDWARE PASSED`, or remains non-Verified with `HARDWARE NOT RUN`/`HARDWARE FAILED`.
3. The diff stays within the agreed scope.
4. No restricted information entered reusable context or shareable output.
5. Remaining limitations and risks are documented.

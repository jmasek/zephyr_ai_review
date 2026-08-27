---
name: spotflow-pr-review-contract
description: GitHub PR review contract defining evidence, severity, finding attribution, residual gaps, and read-only rules. Use ONLY for the focused multi-agent `/spotflow-pr-review` workflow.
---

# Spotflow PR Review Contract

Follow this contract exactly when reviewing a Spotflow Zephyr pull request as a specialist or coordinating the final review.

## Workspace Discovery

- Run `west topdir` to locate the west workspace.
- Read `west config manifest.path` and resolve it relative to the west top directory to locate the Spotflow repository.
- Use `west list zephyr -f "{abspath}"` to locate upstream Zephyr.
- Treat the resolved Spotflow repository's `zephyr/` directory as the reviewed module.
- Never assume a fixed Spotflow checkout path.

## Review Scope

- Run GitHub and Git commands against the resolved Spotflow repository.
- Read and follow repository-local `AGENTS.md` files when present and relevant coding guidance such as `CODING_STYLE.md`.
- Use `gh` to inspect PR metadata, description, base and head, every included commit, the complete base-to-head diff, changed files, checks, existing comments, reviews, and unresolved threads.
- Inspect relevant surrounding implementation, callers, peer subsystems, tests, configuration, samples, upstream Zephyr implementation, and documentation.
- Review the entire pull request, not only its latest commit.
- Establish that each finding is introduced or exposed by the pull request.
- Do not report pre-existing issues unless the pull request directly worsens or newly exposes them.
- Do not suggest unrelated refactors, stylistic preferences, speculative improvements, or behavior outside the stated design.
- Do not edit files, post comments, or submit a GitHub review.

## Finding Format

Every finding must name the exact specialist subagent that reported it. Specialists number their candidate findings sequentially from 1. During final synthesis, discard candidate numbering and number all retained findings sequentially from 1 in severity order.

Format every finding exactly as follows:

`<number>. [severity] path:line`
- Specialist: `<full specialist subagent name>`
- Trigger: exact input, configuration, boundary value, or failure sequence.
- Evidence: implementation path demonstrating the defect.
- Impact: device, cloud, build, or user effect.
- Fix: smallest correct correction.
- Verification: exact test, build, or configuration.

Example:

`1. [High] zephyr/src/net/spotflow_processor.c:145`
- Specialist: `spotflow-reliability-reviewer`
- Trigger: The MQTT connection drops after a queued item is removed but before publish completion is recorded.
- Evidence: The processor removes the item before calling the publish path, and the disconnect path does not requeue or retain it.
- Impact: The device permanently loses the queued telemetry item.
- Fix: Retain ownership until publish completion or restore the item on this failure path.
- Verification: Add a disconnect-during-publish test and verify that the item is retried after reconnection.

Use the changed line responsible for the issue whenever possible. If responsibility lies in unchanged surrounding code exposed by the pull request, identify the closest relevant line and explain the connection in Evidence.

## Severity

- Critical: security compromise, corruption, persistent crash, or broad outage.
- High: a supported build failure, probable runtime failure, data loss, or cloud protocol break.
- Medium: a reproducible correctness or resilience problem with limited scope, reach, or workaround.
- Low: a concrete maintainability, readability, documentation, or coverage defect that materially raises regression risk.

Do not inflate severity. Style preferences alone are not findings.

## Verification

- Validate every finding against the diff and surrounding code before returning it.
- Run proportionate targeted verification when feasible and permitted.
- Follow build, test, formatting, and Kconfig guidance in the Spotflow repository.
- Do not claim a command ran unless it completed. State exactly what was and was not run.
- A Verification field may prescribe a new test that does not exist, but must describe its exact intended behavior.

## Residual Coverage Gaps

Return concrete residual coverage gaps even when no actionable issue is found. A residual gap is an important behavior, target, configuration, boundary, or failure path that available tests, builds, or evidence do not establish; it is not automatically a finding.

Use `None identified` only after explicitly checking for relevant gaps.

## Specialist Result

Return exactly one result with these sections:

```markdown
## Specialist Status

- Specialist: <full specialist subagent name>
- Ran: Yes
- Issues found: <count before synthesis>

## Findings

<locally numbered findings in the mandatory format, or `No actionable findings.`>

## Residual Coverage Gaps

<concrete gaps, or `None identified`>

## Verification Performed

<exact commands and inspections performed, including anything not run>
```

The issue count must equal the number of formatted findings. Never report `Ran: Yes` unless the assigned review was actually completed.

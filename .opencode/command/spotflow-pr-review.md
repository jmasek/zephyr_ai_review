---
description: Run a mandatory eleven-agent review of a Spotflow Zephyr GitHub PR.
agent: build
---

Review Spotflow Zephyr GitHub pull request `$ARGUMENTS` using the mandatory focused multi-agent workflow below.

## Input

`$ARGUMENTS` must be a GitHub pull request number or URL. If it is missing or invalid, stop and ask for a valid pull request number or URL.

## Workspace Discovery

Run `west topdir` to locate the west workspace. Read `west config manifest.path` and resolve that path relative to the west top directory to locate the Spotflow Device SDK repository. Use `west list zephyr -f "{abspath}"` to locate upstream Zephyr.

The Spotflow repository is the GitHub and Git repository under review. Its `zephyr/` directory is the reviewed module. Never assume that the Spotflow repository is named `spotflow` or located at `modules/lib/spotflow`.

## Preparation

Load and follow the `spotflow-pr-review-contract` skill. Read repository instructions and coding guidance in the Spotflow repository when present.

Use `gh` and Git against the resolved Spotflow repository to establish:

- Pull request title, description, state, author, and labels
- Base and head branches and commit SHAs
- Every commit included in the pull request
- Complete base-to-head changed-file list and diff
- Checks, existing comments, submitted reviews, and unresolved threads

Do not edit files, post comments, or submit a GitHub review unless explicitly requested. This command performs a read-only review.

## Mandatory Agent Execution

Launch ALL eleven named subagents below. Each entry is mandatory:

| Specialist | Required subagent type |
|---|---|
| Architecture and lifecycle | `spotflow-architecture-reviewer` |
| Memory safety and sizing | `spotflow-memory-reviewer` |
| Performance and resource efficiency | `spotflow-performance-reviewer` |
| C and Zephyr correctness | `spotflow-zephyr-reviewer` |
| MQTT, CBOR, TLS, and cloud protocol | `spotflow-protocol-reviewer` |
| Kconfig, CMake, devicetree, and portability | `spotflow-kconfig-reviewer` |
| Public API and compatibility | `spotflow-api-reviewer` |
| Reliability and diagnostics | `spotflow-reliability-reviewer` |
| Verification completeness | `spotflow-verification-reviewer` |
| Readability and maintainability | `spotflow-readability-reviewer` |
| Documentation and terminology | `spotflow-documentation-reviewer` |

Use the task tool with each exact `subagent_type`. Launch all eleven concurrently in one fan-out when tool limits permit. If tool limits restrict concurrent calls, launch consecutive parallel batches until every named subagent has run.

Do not skip, merge, replace, rename, or simulate a specialist. Do not use `general` or `explore` in place of a named specialist. The coordinator's own inspection does not count as a specialist run.

Give every specialist a task containing:

- Pull request number or URL `$ARGUMENTS`
- Resolved west top directory, Spotflow repository root, Spotflow `zephyr/` module, and upstream Zephyr path
- Base and head commit SHAs
- Pull request title and description
- An explicit instruction to load and follow `spotflow-pr-review-contract`
- An explicit instruction to inspect the complete pull request independently through its assigned focus
- An explicit instruction to return the mandatory specialist status, findings, residual gaps, and verification sections

Each specialist must inspect the diff and relevant repository context itself. Do not ask a specialist to review only a coordinator-generated summary.

Wait for ALL eleven specialist tasks to complete. If a task fails, times out, or returns without the mandatory result sections, retry or resume that same named specialist. Do not begin final synthesis while any specialist is missing or incomplete.

For every specialist, retain:

- Whether it actually completed
- Its candidate issue count
- Every candidate finding and its full specialist subagent name
- Every concrete residual coverage gap, including when it found no issue
- Verification it performed or could not perform

## Finding Validation

After all eleven specialists complete, independently validate every candidate finding against the base-to-head diff and relevant surrounding code.

- Remove false positives and issues not introduced or exposed by the pull request.
- Deduplicate overlapping findings while preserving the strongest trigger and evidence.
- Reconcile conflicting specialist conclusions.
- Preserve the full specialist subagent name on each retained finding.
- Number retained findings monotonically from 1 after deduplication, in Critical, High, Medium, then Low order.
- Keep every retained finding in the exact format required by `spotflow-pr-review-contract`.
- Do not turn a residual coverage gap into a finding unless it independently satisfies the finding contract.

## Final Response

Begin with this execution table and replace every placeholder with an actual value:

| Specialist | Ran | Issues found | Retained after synthesis |
|---|---:|---:|---:|
| Architecture and lifecycle | Yes | count | count |
| Memory safety and sizing | Yes | count | count |
| Performance and resource efficiency | Yes | count | count |
| C and Zephyr correctness | Yes | count | count |
| MQTT, CBOR, TLS, and cloud protocol | Yes | count | count |
| Kconfig, CMake, devicetree, and portability | Yes | count | count |
| Public API and compatibility | Yes | count | count |
| Reliability and diagnostics | Yes | count | count |
| Verification completeness | Yes | count | count |
| Readability and maintainability | Yes | count | count |
| Documentation and terminology | Yes | count | count |

Every specialist row must explicitly state whether that named subagent ran and how many issues it found. `Issues found` is the count returned by that specialist before deduplication. `Retained after synthesis` is the number of final findings attributed to that full specialist subagent name.

Every `Ran` value must be `Yes` before producing a normal review result. Never claim `Yes` for an agent that was skipped, simulated, replaced, or did not complete. If any specialist remains incomplete, do not produce findings or a normal review summary. State which specialist is incomplete, run it, and then continue synthesis.

Then return these sections:

### Findings

List validated findings in the exact contract format, numbered monotonically from 1. If none remain, state `No actionable findings.`

### Residual Coverage Gaps

Create one labeled entry for each of the eleven specialists using its full subagent name. Preserve concrete residual coverage gaps even when it found no issue or all its findings were removed during synthesis. Use `None identified` only when that specialist explicitly reported it.

### Open Questions

List only questions that could change correctness or the review conclusion. If none, state `None`.

### Verification Performed

Summarize exact commands and inspections performed by the coordinator and specialists. Clearly identify builds or tests that were not run.

### Review Summary

Briefly state overall risk, affected behavior, and the most important residual uncertainty.

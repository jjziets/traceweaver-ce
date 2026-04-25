---
date: 2026-04-25
topic: ce-traceability-lifecycle-control
---

# CE Traceability Lifecycle Control

## Problem Frame

Compound Engineering already helps agents brainstorm, plan, work, review, and compound learning. The missing control is lifecycle traceability: agents can still create or modify behavior without preserving a clear chain from stakeholder intent to requirement, design decision, implementation, verification, and validation.

The failure mode is broader than dark code. The workflow should also help identify code that is unused, unneeded, duplicate, obsolete, overbuilt, unverified, or unvalidated. The goal is not to make agents produce more documentation. The goal is to make agentic development easier to audit, easier to change, and less likely to accumulate uncontrolled complexity.

---

## Actors

- A1. Human engineer: Defines intent, approves inferred requirements, decides unresolved traceability gaps, and makes final merge decisions.
- A2. Planning agent: Converts requirements into implementation units and must preserve requirement IDs into the plan.
- A3. Work agent: Implements scoped changes and must maintain requirement-to-code and requirement-to-evidence links while working.
- A4. Review agents: Review documents or code from specialized perspectives and must respect traceability constraints relevant to their scope.
- A5. Traceability reviewer agent: Audits the requirement chain, flags orphaned behavior and lifecycle gaps, and classifies untraced or unnecessary code candidates.
- A6. Compound-learning agent: Captures recurring traceability failures and useful remediation patterns in `docs/solutions/`.

---

## Key Flows

- F1. Requirements-to-plan trace
  - **Trigger:** A user asks `ce-plan` to plan work from a requirements document or traceability matrix.
  - **Actors:** A1, A2
  - **Steps:** The planning agent reads the requirements source, identifies stable requirement IDs, creates implementation units that cite those IDs, and records expected verification and validation paths.
  - **Outcome:** The plan can be reviewed without guessing which requirement each unit satisfies.
  - **Covered by:** R1, R4, R8, R9, R10

- F2. Plan-to-code trace
  - **Trigger:** A user asks `ce-work` to execute a plan.
  - **Actors:** A2, A3, A5
  - **Steps:** The work agent reads the plan and traceability matrix, implements only scoped behavior, records implementation references, and stops when meaningful behavior lacks a requirement, design decision, risk control, or approved gap.
  - **Outcome:** New or changed behavior has a documented source of authority before review.
  - **Covered by:** R1, R4, R9, R11, R15

- F3. Traceability review before merge
  - **Trigger:** A user asks `ce-code-review` to review a meaningful behavior change.
  - **Actors:** A4, A5, A1
  - **Steps:** The code-review workflow includes a traceability reviewer when the diff affects behavior, public interfaces, risk controls, tests, validation, or cleanup. The traceability reviewer checks forward and backward links and flags inferred links requiring human approval.
  - **Outcome:** Merge review includes systems-engineering evidence, not only code correctness.
  - **Covered by:** R2, R3, R12, R13, R14

- F4. Cleanup and obsolete-code audit
  - **Trigger:** A refactor, simplification, or review suggests code may be unused, duplicated, obsolete, or unnecessary.
  - **Actors:** A3, A5, A1
  - **Steps:** The agent identifies candidate code, checks whether traceability exists, classifies the candidate, and asks for human approval before deleting behavior whose trace may have been lost.
  - **Outcome:** Cleanup decisions distinguish truly unnecessary code from useful code with missing traceability.
  - **Covered by:** R2, R6, R13, R14

- F5. Compound traceability learning
  - **Trigger:** A traceability review finds a recurring gap or a successful remediation pattern.
  - **Actors:** A5, A6
  - **Steps:** The agent records the pattern with cause, impact, fix, and future detection cues.
  - **Outcome:** Future planning and review agents can reuse traceability lessons instead of rediscovering them.
  - **Covered by:** R16

---

## Requirements

**User requirements**

- R1. `UREQ-TRACE-001`: A human reviewer can follow the chain from intent to stakeholder need, requirement, design decision, implementation, verification, and validation for every meaningful behavior change.
- R2. `UREQ-TRACE-002`: A human reviewer can identify candidates for untraced, unused, unneeded, duplicate, obsolete, overbuilt, unverified, or unvalidated code before merge or cleanup.
- R3. `UREQ-TRACE-003`: A human reviewer can distinguish approved trace links from inferred links that require human confirmation.
- R4. `UREQ-TRACE-004`: The workflow remains lightweight by tracing meaningful behavior, interfaces, risk controls, tests, validation scenarios, and cleanup decisions rather than every private helper function.
- R5. `UREQ-TRACE-005`: Review agents can use a shared traceability matrix as the audit record for their scoped review without needing to reconstruct intent from scattered conversation history.

**System requirements**

- R6. `SREQ-TRACE-001`: Add a `ce-traceability` skill that defines the lifecycle traceability workflow, no-orphan behavior gate, matrix authority boundary, evidence expectations, cleanup classifications, and validation gate.
- R7. `SREQ-TRACE-002`: The Markdown traceability matrix is authoritative for trace links, ownership, status, evidence references, gaps, and human decisions; source artifacts remain authoritative for detailed requirement text, plan units, ADR rationale, ATP procedures, and measured result records.
- R8. `SREQ-TRACE-003`: The traceability matrix supports stable IDs for needs, user requirements, system requirements, design decisions, implementation references, verification evidence, validation evidence, ATP entries, result records, risks, gaps, and cleanup candidates.
- R9. `SREQ-TRACE-004`: When traceability context is requested or already present, `ce-brainstorm` captures or preserves user/system requirement IDs and records or links the active traceability matrix path so downstream agents can find it instead of inventing a path.
- R10. `SREQ-TRACE-005`: `ce-plan` reads requirement and traceability artifacts when present and carries requirement IDs into implementation units, acceptance criteria, ATP entries, verification paths, validation paths, and likely file scopes.
- R11. `SREQ-TRACE-006`: `ce-work` reads requirement and traceability artifacts when present, implements against requirement-linked units, records implementation, ATP/result, and evidence updates, and treats a task ID alone as insufficient authority.
- R12. `SREQ-TRACE-007`: `ce-doc-review` can review requirements, plans, ADRs, ATPs, result records, and traceability matrices for missing IDs, broken links, unclear authority, fake traceability, and validation gaps.
- R13. `SREQ-TRACE-008`: `ce-code-review` conditionally includes a traceability reviewer for meaningful behavior changes, public interface changes, risk-control changes, cleanup/refactor changes, or diffs that introduce tests without clear requirement coverage.
- R14. `SREQ-TRACE-009`: The traceability reviewer classifies candidates as `keep-and-document`, `test-and-verify`, `validate`, `deduplicate`, `deprecate`, `remove`, or `escalate`, and must not recommend deletion solely because traceability is missing.
- R15. `SREQ-TRACE-010`: Agents add trace references in the lowest-noise location that helps later review: plan units, traceability matrix rows, ATP/result records, PR summaries, test names, or targeted code comments for externally meaningful behavior. Agents must not spam requirement IDs across every helper function.
- R16. `SREQ-TRACE-011`: `ce-compound` can capture traceability learnings, including recurring orphan-behavior patterns, unnecessary-code patterns, duplicate-code patterns, obsolete-code patterns, and useful remediation strategies.
- R17. `SREQ-TRACE-012`: Validation compares a baseline CE workflow against a traceability-enabled workflow on at least two realistic tasks and blocks packaging if the new layer only adds process without improving engineering clarity.

---

## Acceptance Examples

- AE1. **Covers R1, R7, R9, R10, R11.** Given a requirements doc with `SREQ-AUTH-001`, when `ce-plan` creates a unit and `ce-work` implements it, the plan unit, implementation reference, ATP/result record, test evidence, and validation path all cite the same requirement chain.
- AE2. **Covers R3, R11, R15.** Given a task labeled `TASK-123: Add caching layer` with no linked requirement, ADR, risk control, or approved gap, when `ce-work` starts implementation, the behavior is still treated as orphaned and the agent asks for authority instead of coding.
- AE3. **Covers R2, R13, R14.** Given two modules that perform the same externally meaningful behavior but only one traces to a current requirement, when `ce-code-review` runs, the traceability reviewer flags a duplicate-code candidate and recommends deduplication or documentation rather than silently accepting both.
- AE4. **Covers R2, R13, R14.** Given legacy behavior with tests but no current requirement, validation path, or owner, when a cleanup review runs, the reviewer classifies it as obsolete or lost-traceability candidate and asks for a human decision before removal.
- AE5. **Covers R3, R12, R13.** Given an agent inferred that an existing file satisfies `SREQ-TRACE-004`, when doc or code review runs, the inferred link is marked as requiring human approval rather than promoted to verified traceability.
- AE6. **Covers R4, R15.** Given a private helper added inside a traced module, when the helper only supports an already-traced behavior, the agent does not create a separate requirement row and instead lets the helper inherit traceability through the owning behavior.

---

## Success Criteria

- Agents working through `ce-brainstorm`, `ce-plan`, `ce-work`, `ce-doc-review`, `ce-code-review`, and `ce-compound` preserve or explicitly update requirement IDs and traceability evidence when the change affects meaningful behavior.
- Review output can answer: why does this exist, what requirement does it satisfy, what design decision supports it, where is it implemented, how is it verified, how is it validated, and what breaks if it is removed.
- Cleanup/refactor reviews surface unused, unneeded, duplicate, obsolete, and overbuilt code candidates with enough evidence for a human to decide.
- Traceability remains lightweight: small obvious changes can use compact trace notes, while larger behavior changes use a matrix, ATP/result records, and validation evidence.
- Validation shows distinct value beyond existing CE workflows before any upstream PR is packaged.

---

## Scope Boundaries

- Do not update all CE agents in the first implementation. Patch the orchestrating skills first: `ce-plan`, `ce-work`, `ce-doc-review`, `ce-code-review`, and `ce-compound`.
- Do not make every helper function carry a direct requirement ID. Trace meaningful behavior and let internal helpers inherit traceability through the behavior, module, interface, or risk control they support.
- Do not make the matrix the source of detailed requirement, ADR, ATP, or result content. The matrix is the audit record for links and status.
- Do not delete code merely because traceability is missing. Missing traceability creates a candidate and a human decision, not automatic removal authority.
- Do not build full MBSE, SysML, or a requirements database in the first implementation.
- Do not add broad automation or static-analysis enforcement until the manual skill and reviewer workflow has been validated.
- Do not package an upstream PR if validation shows the traceability layer duplicates existing CE behavior without distinct findings.

---

## Key Decisions

- Use Compound Engineering as the primary integration target because it already has multi-agent document review, code review, work execution, and compounding workflows.
- Keep the first implementation as a focused vertical slice: one traceability skill, one traceability reviewer agent, one matrix template, and minimal hooks into core orchestrators.
- Treat the matrix as an audit record, not as the requirement database.
- Treat traceability as lifecycle control, not an end-of-project reconstruction exercise.
- Treat cleanup as part of traceability: unused, unnecessary, duplicate, obsolete, and overbuilt code are system-maintenance risks, not only style concerns.
- Prefer structured trace notes in artifacts over noisy code comments. Code comments with requirement IDs are reserved for externally meaningful behavior, risk controls, public interfaces, or non-obvious design constraints.

---

## Dependencies / Assumptions

- The CE plugin continues to use Markdown skills, flat `ce-*` agent files, and `docs/brainstorms/` plus `docs/plans/` as durable planning artifacts.
- The implementation can use CE's existing subagent orchestration patterns rather than inventing a new multi-agent framework.
- Existing plan U-IDs and origin R/A/F/AE traceability work remain compatible with this broader traceability layer.
- `bun run release:validate` remains the required plugin validation command before any contribution is considered ready.

---

## Outstanding Questions

### Resolve Before Planning

- None.

### Deferred to Planning

- [Affects R6, R8][Technical] Define the exact Markdown matrix template and whether it lives under `ce-traceability/references/` only, or is also surfaced in README examples.
- [Affects R9][Technical] Define the discoverable matrix path/link convention and fallback behavior when multiple traceability matrices exist.
- [Affects R10, R11][Technical] Decide the minimal hook wording for `ce-plan` and `ce-work` so they respect traceability without bloating every invocation.
- [Affects R12, R13][Technical] Decide whether the traceability reviewer is always included for standard/deep reviews or only conditionally triggered by behavior-change signals.
- [Affects R15][Technical] Define the exact trace-reference convention for tests, PR summaries, work summaries, and targeted code comments.
- [Affects R17][Technical] Select two validation scenarios, likely one CE plugin change and one Vestro refactor, and define baseline inputs before running comparisons.

---

## Next Steps

-> `ce-doc-review` for requirements review.
-> `ce-plan` for structured implementation planning after review findings are resolved.

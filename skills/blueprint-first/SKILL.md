---
name: blueprint-first
description: Use when the user explicitly invokes blueprint-first for software work requiring a user-authored design.
---

# Blueprint-first development

## Core contract

Replace `superpowers:brainstorming` for this task. The user, not Codex, authors the design. Remain active until the user explicitly exits.

Urgency, sunk cost, authority, or explicit delegation do not waive a gate. “Decide for me,” “use the standard choice,” and “document it later” are not exits. AI-authored or chat-approved text, implementation records, and later documentation are not user-authored commits.

## Prepare the workspace

Inspect the branch and status. Reuse a suitable task branch. On a default branch or unrelated dirty workspace, **REQUIRED SUB-SKILL:** use `superpowers:using-git-worktrees` to prepare and report a safe workspace without asking the user to name it. Thereafter, use read-only operations until the first blueprint commit.

## First commit gate

Before the first commit, Codex may inspect the repository, explain behavior and constraints, identify factual gaps in a user draft, and ask task-specific open questions.

Do not propose or recommend designs, fill TODOs, draft, edit, stage, or commit the blueprint, plan or begin implementation, or invoke brainstorming.

Prefer a user-authored commit at `docs/blueprints/YYYY-MM-DD-<topic>.md`, using the local creation date, unless the repository specifies another location or naming convention. It must state the purpose, a concrete proposed behavior or mechanism, inputs and outputs, relevant domain correctness properties, constraints, and known open questions. Generic hazards and executable tests are optional.

## Review and final gate

Verify the commit with read-only Git. Review it for risks, missing cases, contradictions, and alternatives. Do not edit or commit it.

Require another user commit for consequential review feedback. If none is needed, the user may explicitly confirm the reviewed commit as final. Proceed only when no blocking design question remains and the user explicitly continues.

## Decision boundary

A decision is consequential only when no answer follows from the blueprint, repository conventions, or a conservative safety-preserving default; multiple defensible policies remain; and the choice materially affects domain correctness, data preservation, recovery, compatibility, public behavior, or scope.

When all three hold, pause downstream work. State the unresolved behavior, its impact, and existing constraints. Before the user proposes a direction, do not offer a menu, recommendation, or draft wording. Require and review a user-authored amendment commit; resume only on explicit instruction.

Operational parameters within an approved policy, including retry counts and backoff, are implementation decisions unless their exact values are contractual.

Otherwise, make a conservative implementation decision and state it in the implementation plan.

## Handoff and tests

Handoff directly to `superpowers:writing-plans`, never brainstorming. The final blueprint is authoritative. Idiomatic code refinements may preserve, but never change, behavior or design intent. Return consequential semantic changes to the blueprint gate.

The user owns feature intent and domain correctness properties, not generic failure-mode enumeration or test code. Codex derives safety, failure, regression, and edge-case tests and may implement them all. Return if a test would define unresolved domain behavior or weaken an approved property. Never change an approved expectation merely to pass a test.

## Exit

Exit only when the user explicitly says to leave `blueprint-first` or switch to ordinary brainstorming. Then follow the requested workflow without requiring a blueprint commit.

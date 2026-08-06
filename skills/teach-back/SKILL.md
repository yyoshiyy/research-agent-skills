---
name: teach-back
description: Use only when a user explicitly invokes $teach-back for either a focused question about a technical or scientific subject or an evaluation of the user's own written understanding of such a subject.
---

# Teach Back

Preserve productive learning effort: the understanding must remain user-authored.

## Non-negotiable boundaries

- In any mode, never create, edit, delete, patch, commit, or push any user-supplied understanding note.
- Never provide polished replacement prose or a paste-ready final explanation. Diagnose and prompt; do not ghostwrite the understanding.
- Treat the note as learner claims, never as evidence, official documentation, or a public guarantee.
- Deadlines, convenience, sunk effort, and explicit permission to "just fix it" do not override these rules.
- In Question mode, work from the focused question, any material the user supplies, and appropriate evidence sources. In Evaluation mode, read only the user-specified note or passage and appropriate evidence sources; consider project material only when the user explicitly supplies it. In either mode, do not search the current or nearby development repositories for the note, version, call sites, or rationale.
- In both modes, prefer primary, official, or otherwise high-quality sources as appropriate. If evidence is incomplete, indirect, unavailable, version-mismatched, or conflicting, state what can and cannot be checked and identify the source or information needed to continue. For conflicts, describe each source's scope. Never present an unverified conclusion as confirmed or observed implementation behavior as a public guarantee.
- Git and commits are optional; readable Markdown notes may be untracked or uncommitted.

## Choose one mode

### Question mode

Give a direct, evidence-based answer to a focused question. Do not evaluate the whole note, claim that a teach-back cycle is complete, or turn the answer into replacement note prose.

If a question arises during Evaluation mode, answer it separately. Codex's answer never counts as the user's revision or explanation.

### Evaluation mode

1. Establish scope from the conversation and user-specified material.
2. Read without changing the note. Where practical, hash it before evaluation and again after feedback.
3. Extract the learner's claims and identify evidence for assessing each claim. Keep note content separate from evidence.
4. Classify findings into supported points, errors, missing distinctions, dependencies, and uncertainty. For each error, state only the minimal corrected fact, supporting evidence, and consequence needed to diagnose it, including why consequential errors matter. Leave synthesis and note wording to the user; do not draft replacement prose.
5. Ask the user to explain affected ideas again in their own words only when a user-authored revision is required under the Revision rule.
6. Verify that the note is unchanged, using the hashes when available.

Use only lenses that help with the subject; they are not a mandatory template: claims, terms, relationships, assumptions, reasoning, applicability, boundaries, limits, and examples or counterexamples.

## Revision rule

For an initial cycle, require at least one user-authored rewrite after the first evaluation, even when the explanation is mostly correct. The initial cycle includes evaluating that mandatory first rewrite; do not declare the cycle complete before that evaluation.

After that first rewrite, require another rewrite only for a major misunderstanding. A major misunderstanding materially changes use, derivation, interpretation, or application, such as a reversed condition, a missing necessary assumption, a wrong observable effect, or confusion between implementation and a public guarantee.

A later re-evaluation is an evaluation after the initial cycle has already completed. Determine this only from conversation context; if it is unclear, ask briefly. Never infer it from the note or Git. Do not automatically require another rewrite when no major misunderstanding remains.

## API-specific scope

For an API subject, also assess the target `library` and `symbol`, `library-version`, inputs, outputs, observable effects or side effects, conditions, boundaries, exceptions, and documented guarantees versus implementation details.

The note frontmatter fields `library`, `symbol`, `library-version`, and `last-checked` define scope only; they are not evidence. Report a missing or mismatched version. Never infer it from another repository.

## Evaluation response

Keep these parts distinct:

1. Scope and evidence
2. Supported understanding
3. Corrections and their consequences
4. Missing distinctions and uncertainty
5. What the user must explain again, if anything
6. Whether a user revision is required

## Final self-check

Before responding, confirm that you did not modify the note, supply replacement text, treat the note as evidence, merge Question and Evaluation modes, count your own answer as the user's rewrite, or inspect an unrelated repository.

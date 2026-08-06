# Teach-back Skill Design

Date: 2026-08-06
Status: Approved

## Purpose

The `teach-back` skill helps a user deepen and maintain their own
understanding of a technical or scientific subject.

The user writes an explanation in their own words. Codex then compares
the claims in that explanation with appropriate evidence and identifies
correct points, misconceptions, missing distinctions, and remaining
uncertainty.

The skill deliberately preserves productive learning friction. It must
not turn the user's understanding note into an AI-authored document.

## Goals

- Make the user express their current understanding in their own words.
- Compare that understanding with appropriate external evidence.
- Help the user identify and correct consequential misunderstandings.
- Require at least one user-authored revision during an initial
  teach-back cycle.
- Allow later re-evaluation when the user updates an existing note.
- Support local, uncommitted notes as well as notes managed with Git.
- Work across multiple subject types through a common teach-back core.
- Add subject-specific evaluation criteria when they materially improve
  the evaluation.
- Remain inactive unless the user explicitly invokes the skill.

## Non-goals

The skill does not:

- write, rewrite, complete, or reorganize the user's understanding note;
- provide a replacement explanation intended to be pasted into the note;
- create patches for the note;
- commit or push the note;
- treat the note as documentation or evidence;
- certify that the user's understanding is permanently complete;
- track scores, proficiency levels, or learning-method labels;
- require a particular note repository or directory structure;
- depend on the software repository from which the learning question
  originated.

## Invocation policy

The skill is explicit-only.

Its `agents/openai.yaml` must contain:

```yaml
policy:
  allow_implicit_invocation: false
```

Codex must not start the teach-back workflow merely because a user asks
a technical question or discusses their understanding. The user must
explicitly invoke `$teach-back`.

Explicit invocation is required on every turn that should apply the
skill. When Codex asks the user to revise an explanation, it must tell
the user to invoke `$teach-back` again when submitting that revision;
conversation context alone does not reactivate the skill.

## User-owned understanding notes

An understanding note is written and maintained by the user.

Codex may read a user-specified note for question answering or
evaluation, but it must not modify the note under any circumstances
while operating under this skill.

This prohibition includes:

- creating the note;
- editing or deleting any part of it;
- producing a patch for it;
- supplying a polished replacement paragraph;
- filling in a note template on the user's behalf;
- committing or pushing changes.

Codex may give concise factual corrections because those are necessary
for evaluation. It must present them as diagnostic feedback rather than
as ready-to-paste note text.

## Note location and version history

The skill does not prescribe where understanding notes are stored.

Git-based history is recommended because it lets the user revisit and
compare earlier explanations, but Git is not required. A readable,
user-specified local Markdown file is sufficient for evaluation,
including when the file is untracked or has uncommitted changes.

A commit is never a prerequisite for asking a question or requesting an
evaluation.

The skill does not require the note to record workflow state, learning
method, evaluation count, or completion status.

## Repository independence

The skill must not automatically inspect the current working
repository, nearby repositories, or an assumed consuming application.

Its normal scope is limited to:

- the note explicitly identified by the user;
- sources explicitly supplied by the user;
- appropriate upstream or reference sources needed to answer the
  question or perform the evaluation.

Project-specific usage, call-site behavior, or design rationale is
outside the normal scope unless the user explicitly supplies that
material and asks for it to be considered.

The absence of a related development repository must not prevent the
skill from operating.

## Two operating modes

### Question mode

Question mode answers a focused question about the subject.

It may be used:

- before the user writes a complete explanation;
- while the note contains uncommitted changes;
- between evaluation rounds;
- when the user encounters a specific point they cannot resolve.

In question mode, Codex may provide a direct, evidence-based answer.
It may read a user-specified passage for context, but it does not
evaluate the whole note or declare the teach-back cycle complete.

Answering a question does not count as evaluating the user's
understanding.

### Evaluation mode

Evaluation mode begins only when the user explicitly asks Codex to
evaluate a user-authored understanding note.

Codex must:

1. identify the claims made by the user;
2. determine which claims can be checked;
3. select appropriate evidence and record enough source information for
   the user to identify and inspect it;
4. compare the claims with that evidence;
5. distinguish errors from missing detail and unresolved uncertainty;
6. give diagnostic feedback;
7. prompt the user to explain the subject again when a revision is
   required, and tell the user to invoke `$teach-back` again in the
   response that submits the revision.

Codex must not convert the evaluation into an AI-written replacement
note.

If the user asks a focused question during evaluation, Codex answers it
as a separate question-mode exchange. The answer does not itself count
as a revised explanation or a completed evaluation.

## Evidence policy

The understanding note is always treated as a collection of learner
claims. It is never treated as:

- an official specification;
- an authoritative reference;
- an API guarantee;
- evidence that a claim is correct.

Suitable evidence depends on the subject and may include:

- official documentation;
- published specifications or standards;
- versioned source code;
- original papers;
- established textbooks or reference works;
- other clearly identified primary or high-quality sources.

For every material supported point and correction, Codex must give an
identifiable source: its title or responsible organization and a URL or
other stable identifier, the relevant version or publication date when
applicable, and the relevant section, heading, page, or symbol when each
applies and is available. If applicable source metadata is unavailable,
Codex must state that limitation instead of silently omitting it. Vague
attribution such as "the official documentation says" is insufficient.
Sources should appear next to the claims they support.

Codex must state when evidence is incomplete, indirect, unavailable, or
version-mismatched. It must not silently convert observed implementation
behavior into a public guarantee.

When sources disagree, Codex should describe the disagreement and the
scope of each source instead of presenting false certainty.

## Common evaluation criteria

The common teach-back core should consider, where relevant:

- the central claim or concept;
- the meaning of important terms;
- relationships between concepts;
- assumptions and preconditions;
- causal or logical reasoning;
- applicable and inapplicable cases;
- boundaries and limitations;
- examples and counterexamples;
- distinctions the user may have collapsed;
- uncertainty that cannot be resolved from the available evidence.

These are evaluation lenses, not a mandatory note template. The skill
must not require every note to use the same headings.

## Initial rewrite rule

During an initial teach-back cycle, the user must revise or explain the
subject again at least once after receiving the first evaluation.

Codex must not declare the initial cycle complete immediately after its
first evaluation, even when the initial explanation is mostly correct.

After the user's first revision:

- request another revision only if a major misunderstanding remains;
- report minor inaccuracies or useful refinements without forcing
  meaningless repetition;
- allow the cycle to stop when no major misunderstanding remains.

A major misunderstanding is one that would materially change how the
subject is used, derived, interpreted, or applied. Examples include
reversing a condition, misunderstanding an observable effect, omitting
a necessary assumption, or confusing implementation behavior with a
public guarantee.

## Later re-evaluation

The user may request evaluation again after voluntarily updating an
older note.

When the user identifies the request as a re-evaluation, Codex must
evaluate the current note without automatically requiring an additional
rewrite merely to satisfy the initial-cycle rule.

If the updated note still contains a major misunderstanding, Codex asks
the user to revise it again. Otherwise, it reports the evaluation
without imposing another edit.

The user may always choose to rewrite and request another evaluation,
even when Codex does not require it.

If the user does not say whether an evaluation is initial or repeated,
Codex should determine this from the current conversation when
possible. If it cannot determine the state reliably, it should ask a
short clarifying question rather than infer a learning history from the
note or Git repository.

## Evaluation feedback

Evaluation feedback should separate:

- what is supported by the evidence;
- what is incorrect;
- what is incomplete or insufficiently distinguished;
- what depends on assumptions or versions;
- what remains uncertain;
- what the user should explain again.

Corrections should identify why a claim is problematic and what
distinction the user needs to reconsider.

Codex should ask focused prompts that require the user to produce the
next explanation. It must not answer those prompts on the user's behalf.

## Subject coverage

The common workflow should work in principle for:

- library APIs, functions, classes, and data structures;
- algorithms;
- formulas and theorems;
- scientific theories and models;
- other technical subjects with checkable claims.

Subject-specific criteria should be applied only when relevant. They
must not turn the common workflow into a rigid universal template.

## Additional criteria for library APIs

When the note concerns a library API, function, class, method, or
similar symbol, the evaluation should additionally consider:

- the library and target symbol;
- the library version against which the claims are evaluated;
- inputs and accepted values;
- outputs and return behavior;
- observable effects and side effects;
- conditions, boundaries, and exceptions;
- differences between documented guarantees and observed
  implementation behavior.

An API-oriented note may use frontmatter such as:

```yaml
---
library: <library-name>
symbol: <symbol-name>
library-version: <version>
last-checked: <date>
---
```

These fields describe the intended scope of the user's note. Their
presence does not make the note a source of evidence.

If version information is missing or inconsistent with the available
sources, Codex should report the resulting uncertainty. It must not
infer the version by inspecting an unrelated development repository.

The API-specific criteria belong in `SKILL.md`; they do not require a
library-specific reference file.

## Other subject types

For algorithms, formulas, theorems, and scientific models, Codex may
adapt the common criteria to the subject. Relevant distinctions may
include preconditions, invariants, assumptions, derivation steps,
interpretation, predictions, domains of applicability, and known
limitations.

These adaptations should remain lightweight. Separate reference files
should be introduced only when repeated use demonstrates that the
common instructions are insufficient.

## Failure and uncertainty handling

If Codex cannot access adequate evidence, it must:

- state what could and could not be checked;
- avoid presenting an unverified conclusion as confirmed;
- identify the source or information needed to continue;
- preserve the distinction between answering a question and evaluating
  the note.

Lack of Git history, lack of a commit, or lack of a related development
repository is not an evaluation failure.

## Skill metadata

The initial metadata should use:

```yaml
interface:
  display_name: "Teach Back"
  short_description: "Deepen understanding through evidence-based teach-back"
  default_prompt: "Use $teach-back to answer a focused question or evaluate my own explanation against evidence without editing my note."

policy:
  allow_implicit_invocation: false
```

## Skill layout

```text
skills/teach-back/
├── SKILL.md
└── agents/
    └── openai.yaml
```

The initial version should keep the skill small. Additional reference
files or scripts should be added only when they solve a demonstrated
problem.

## Validation strategy

The skill should be tested with representative prompts before and after
the skill instructions are applied.

Baseline tests without the skill establish whether Codex would normally
rewrite the note, blur question answering with evaluation, or rely on
irrelevant repository context.

Tests with the skill should cover at least:

1. A request to edit an understanding note.
2. An incorrect explanation that requires diagnostic feedback.
3. A focused question asked while the note is uncommitted.
4. A focused question asked during an evaluation.
5. A missing or mismatched library version.
6. A later re-evaluation of an already updated note.
7. An algorithm-oriented note.
8. A formula- or theorem-oriented note.
9. A scientific theory- or model-oriented note.
10. A situation in which no related development repository is
    available.
11. A normal technical question without explicit skill invocation.
12. An explicit `$teach-back` invocation.
13. An initial cycle in which the user's revision is submitted on a
    later turn and must explicitly invoke `$teach-back` again.
14. An evaluation whose every material supported point and correction
    identifies inspectable sources, including versions or dates when
    relevant.

Acceptance checks should confirm that:

- the note remains byte-for-byte unchanged;
- Codex produces no patch or ready-to-paste replacement;
- question mode and evaluation mode remain distinct;
- the note is not treated as evidence;
- uncommitted notes are accepted;
- Git history is not required;
- the common workflow works outside library APIs;
- API-specific criteria are applied only when relevant;
- nearby development repositories are not explored automatically;
- implicit invocation is disabled;
- explicit invocation remains available.
- required follow-up evaluations instruct the user to invoke
  `$teach-back` again;
- material supported points and corrections include identifiable,
  inspectable sources.

Where practical, file integrity should be checked before and after each
evaluation using a content hash.

## Publication hygiene

Public skill documentation, tests, and examples must not contain:

- the user's actual understanding notes;
- private research or development details;
- names of projects that motivated the skill;
- real symbols or version numbers taken from the user's work;
- local filesystem paths;
- repository-specific call-site information.

Examples should use placeholders or entirely fictional subjects.
Publication review by the maintainer is required before any design
document or skill change is committed or pushed to a public repository.

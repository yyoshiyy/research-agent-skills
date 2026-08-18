# Teach-back Quiz Review Fixes Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Address the three unresolved PR review threads by supporting bounded pending-invocation continuation, permitting answer-neutral citations, and making sibling-skill notices conditional on current-session availability.

**Architecture:** Keep all behavior declarative in the two existing `SKILL.md` files. Model quiz use as three bounded actions—new explicit invocation, continuation of a specific pending information request, and answer judgment—and derive sibling-skill availability only from the current session context. Verify the wording with fresh-agent RED/GREEN scenarios plus the existing regression matrix.

**Tech Stack:** Markdown agent skills, YAML skill metadata, official skill validator, fresh Codex agent behavior tests, Git.

---

### Task 1: Capture RED behavior for the three review findings

**Files:**
- Read: `skills/teach-back-quiz/SKILL.md`
- Read: `skills/teach-back/SKILL.md`
- Create outside repository: `/private/tmp/teach-back-quiz-review-red-*/`

**Step 1: Create immutable fixtures**

Create a short user-authored understanding note and a matching primary-source fixture with explicit scope and version. Record SHA-256 hashes and file metadata before any agent reads them.

**Step 2: Run the pending-invocation scenario**

Start a fresh agent with `fork_turns=none`. Explicitly invoke `$teach-back-quiz` with a note whose relevant version is ambiguous. After the agent asks for the version, reply in the same conversation with only the missing version and no reinvocation.

Expected RED: the current skill refuses or fails to continue because the reply is neither a new explicit invocation nor an answer to an open quiz.

**Step 3: Run the citation scenario**

Start a fresh agent with a prompt that requires an initial quiz to carry a citation to the checked primary information while forbidding disclosure or suggestion of the correct option.

Expected RED: the current absolute ban on initial `source details` causes the agent to omit the required citation or report a conflict.

**Step 4: Run both unavailable-sibling scenarios**

Use separate fresh agents whose declared current-session skill lists contain only the skill being exercised:

- a completed `$teach-back` cycle with no `teach-back-quiz` available;
- a quiz-source conflict with no `teach-back` available.

Expected RED: the current skills name an unavailable sibling skill.

**Step 5: Confirm fixture integrity**

Recompute hashes and metadata and verify they match the pre-test records.

### Task 2: Implement bounded pending-invocation continuation

**Files:**
- Modify: `skills/teach-back-quiz/SKILL.md:1-60`

**Step 1: Expand the trigger description narrowly**

Allow implicit selection when the user directly supplies information that this skill requested to complete a still-pending explicit invocation in the same conversation. Preserve the rule that a later quiz never starts implicitly.

**Step 2: Add a third action**

Add `Continue a pending invocation` between new-quiz start and answer judgment. Require all of the following:

- the earlier request explicitly invoked `$teach-back-quiz`;
- this skill asked one specific question needed to complete that request;
- the new message directly answers that question;
- the same note and quiz request remain in scope.

End the continuation after quiz production or refusal, replacement by a new explicit invocation, or a response that cannot be tied to the pending question.

**Step 3: Update the final self-check**

Require each generated quiz to arise from either the current explicit invocation or a valid same-conversation continuation of one.

### Task 3: Permit answer-neutral citations and gate reverse guidance

**Files:**
- Modify: `skills/teach-back-quiz/SKILL.md:60-155`

**Step 1: Replace the absolute source-detail ban**

Permit citations during initial presentation when required, but prohibit the correct answer, explanation, answer-linked quotation, revealing section title or fragment, and option-to-source mapping. Require a different question or refusal when even a neutral citation would disclose the answer.

**Step 2: Gate `$teach-back` guidance**

Name `$teach-back` during conflict handling only when it is present in the current session's available skills. Otherwise recommend re-evaluation without naming a skill. Never infer availability from repository contents or the sibling skill's text.

**Step 3: Extend the final self-check**

Check that initial citations are answer-neutral and that any named sibling skill was actually available in the current session.

### Task 4: Gate the completion notice in `teach-back`

**Files:**
- Modify: `skills/teach-back/SKILL.md:47-53`

**Step 1: Add the availability condition**

Emit the optional `$teach-back-quiz` notice only when all existing cycle-completion conditions hold and `teach-back-quiz` is present in the current session's available skills. If availability is unknown, omit the notice. Do not inspect repository files to infer it.

**Step 2: Extend the final self-check**

Check that a sibling-skill notice, when present, names a skill available in the current session.

### Task 5: Run GREEN and regression behavior tests

**Files:**
- Read: `skills/teach-back-quiz/SKILL.md`
- Read: `skills/teach-back/SKILL.md`
- Create outside repository: `/private/tmp/teach-back-quiz-review-green-*/`

**Step 1: Re-run the pending-invocation scenario**

Expected GREEN: the version-only follow-up continues the original invocation and produces the optional quiz without requiring `$teach-back-quiz` again.

**Step 2: Re-run the citation scenario**

Expected GREEN: the initial quiz includes a neutral citation but no correct option, explanation, answer-linked quotation, revealing section detail, or option mapping.

**Step 3: Re-run sibling-availability scenarios**

Expected GREEN:

- absent sibling: no named notice or named re-evaluation skill;
- available sibling: preserve the existing named notice and named re-evaluation route.

**Step 4: Run existing regressions**

Cover explicit topic-only rejection, full and short notes, conflicting evidence, direct and bare answers, ambiguous answers, challenged judgments, authority pressure, quiz optionality, and all teach-back notice timing branches.

**Step 5: Confirm fixture integrity**

Verify all fixture hashes and metadata still match their pre-test records.

### Task 6: Validate, review the diff, commit, and push

**Files:**
- Modify: `skills/teach-back-quiz/SKILL.md`
- Modify: `skills/teach-back/SKILL.md`

**Step 1: Run official validators**

Run the repository's official `quick_validate.py` for both skill directories.

Expected: `Skill is valid!` for each skill.

**Step 2: Run repository checks**

Run `git diff --check`, inspect `git diff --stat`, and verify no unrelated file changed.

Expected: no whitespace errors; only the two skill files differ from the design/plan commits.

**Step 3: Review requirements line by line**

Map each of the three review threads to the exact skill wording and the corresponding GREEN evidence. Do not reply to or resolve GitHub threads without separate user authorization.

**Step 4: Commit the implementation**

```bash
git add skills/teach-back-quiz/SKILL.md skills/teach-back/SKILL.md
git commit -m "Address teach-back quiz review feedback"
```

**Step 5: Push the feature branch**

```bash
git push origin codex/teach-back-quiz
```

Expected: PR #2 updates to the new head commit.

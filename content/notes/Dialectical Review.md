+++
title = "Dialectical Review"
date = 2026-02-20
description = "A code review methodology for the age of AI-assisted development."
+++

A code review methodology for the age of AI-assisted development.

## The Problem

Traditional code review doesn't work well anymore. Arguably, it never really worked that well for complex changes. But AI-generated code has dramatically illumated what is wrong with code review. Reviewers scan diffs line by line, leave a few comments about naming or style, rubber-stamp the PR, and move on. The reviewer rarely builds a genuine understanding of what the code should do, and almost never independently verifies that it does exactly and only what it should do. They don't because it is increadibly difficult to reverse-engineer the intent from a line-by-line diff. So nearly no one does.

AI-assisted development has made this problem worse! A developer can produce more code, faster, with comprehensive test suites. But the reviewer's capacity to meaningfully evaluate that output hasn't scaled to match. The original process of reverse-engineering the design from a line-by-line reading of the code was too laborious and cognatively challenging to begin with. If we have to do it more, we never will.

We need a review process where the reviewer's effort produces durable, mechanically verifiable artifacts, not just comments on a diff. And that effort should itself be a creative process, using that reviewers knowledge and background. And the process should consider the open-world space of what else needs to be considered by the changes in the PR, instead of just approving or objecting in the small, line by line.

## The Solution: Dialectical Review

Dialectical Review borrows inspiration from a longstanding intellection tradition in philosophy: "dialectic". The idea is that we arrive at an understanding of deep ideas through conversation or "dialogue" (rame root word). Each person needs to build their own understanding or "mental model", but it's through engaging with the mental model of others that we truely come to understand difficult new ideas.

This tradition goes all the way back to Socrates and the ancient greek philosophers. But Hegel's later form is nicely succint and applicable to our review process:

- **Thesis.** The author writes code and communicates their mental model of what it does and why. But they do not include tests.
- **Antithesis.** The reviewer learns, and then independently challenges that mental model by writing tests designed to test their assumptions or break the code.
- **Synthesis.** Failing tests trigger a conversation. Passing tests become a permanent part of the codebase, collaboratively written by author and reviewer(s).

The key insight is that both test outcomes are valuable. A failing test reveals a gap—either in the code, in the author's communication, or in the reviewer's understanding; in each case, a conversation ensues. A passing test suite means a real human interrogated the code from an independent perspective, the code survived, and the tests record that process in ways that can be mechanically checked. Either way, the codebase and the mental models get stronger.

## The Process

### Phase 1: Author Submits the PR

The author writes the code, with or without AI assistance, and opens a traditional pull request. The PR includes:

- **The code under review.** Production code only. Comments as usual.
- **An educational description.** Not a changelog, not a list of files changed. A genuine explanation of what the code does at a high level, why it exists, what mental model the reviewer needs to hold in order to understand it. Think of it as teaching a colleague, not documenting a commit. Supplement the PR description with additional educational documentation as needed.
- **A code tour.** After describing the high level ideas in prose, guide the user through the code in the order that will help them understand. e.g.: "The entrypoint for a user is Object.method() in X.file." -or- "The types that define the protocol are in package.protocol.types."
- **Interactive demonstration.** Include instructions for how to interact with the proposed changes. e.g.: Call methods manually in a REPL, debugger, or browser console to see intermediate steps and let the reviewer step through the automated process manually and interactively. This is how they can fill in any gaps in their own mental model and test their understanding.
- **No tests.** The author's tests are withheld from the PR. They exist, and they pass, but they are not visible to the reviewer. These tests probably live on a separate branch.

Stripping the tests is deliberate. The reviewer should not be anchored by the author's test cases. The goal is for the reviewer to develop their own sense of what is important and what should be true about this code. The author should teach what the reviewer needs to understand, but do not guide the reviewer into what the author thinks proves the code is right.

### Phase 2: Reviewer Builds a Mental Model

The reviewer reads the PR description and the code. The objective is to understand what the author intended — not to nitpick syntax, not to scan for obvious bugs, but to build a mental model of the system's behavior.

The reviewer should be able to answer:

- What is the purpose of this code?
- What should this code do? 
- What should this code NOT do?
- How do I run it and interactively to see intermediate results or test it with my own values?
- What invariants should this code maintain?
- What are the boundary conditions?
- What assumptions has the author made about inputs, state, and environment?
- What could go wrong that the author might not have considered?
- What should be considered which the author never thought of?

This phase is where the educational PR description pays for itself. The author is not asking the reviewer to reverse-engineer intent from a diff. The author is teaching. The reviewer is learning but bringing all their own ideas into the mix as well.

### Phase 3: Reviewer Writes Adversarial Tests

The reviewer creates a new branch based on the PR branch and writes tests. These tests are adversarial in the legal sense: not hostile, but independently argued. Like a lawyer in a courtroom, the reviewer constructs the strongest case they can that the code is broken, incomplete, or insufficiently specified. That "argument" is made in the form of test cases.

The reviewer should use whatever tools and techniques are appropriate to the situation:

- **Unit tests** for specific behavioral claims.
- **Property-based tests** for invariants that should hold across a wide input space.
- **Model checking** for stateful or concurrent systems.
- **Formal methods** where the stakes justify the effort of achieving proof.

AI assistance is encouraged. The reviewer can and should use Claude Code or any other tool to help generate test cases, identify edge cases, and express properties. AI tools also make using formal methods tooling more accessible and tractible. 

The one constraint is that the reviewer should not be looking at the author's original tests. The reviewer's tests must come from the reviewer's own mental model. That difference in perspective is actually the whole the point of the exercise!

### Phase 4: Reconciliation

Run the reviewer's tests against the author's code.

**If all tests pass:** The reviewer failed to break the code. This is probably a good outcome, assuming strong effort and expertise were exerted in creating the test cases. The reviewer's test suite now represents a meaningful, independently-motivated set of assertions about the code's behavior. These tests should be merged alongside the author's tests.

**If any tests fail:** A failing test is not a defect report. It is the beginning of a conversation. The failure indicates a divergence between the author's mental model (now expressed in code) and the reviewer's mental model (now expressed in tests). There are three possible resolutions:

1. **The code is wrong.** The reviewer found a genuine bug or gap. The author fixes the code. Everyone understands it better.
2. **The test is wrong.** The reviewer misunderstood the intended behavior. The test is corrected or removed, and the PR description is improved to prevent the same misunderstanding in the future. Everyone understands it better.
3. **The spec is ambiguous.** Neither party is wrong. The desired behavior was never clearly defined. The author and reviewer agree on what the behavior should be, update the code and/or tests accordingly, and document the decision. Everyone understands it better.

In all three cases, the codebase is better than it was before and the humans have increasingly robust mental models of both the goals and the code. 

Writing software is a team sport, and the goal is to build a shared mental model, which is expressed in the code.

## Why Strip the Tests?

This is the most counterintuitive part of the process. The author has tests. They pass. Why hide them?

Because visible tests anchor and bias the reviewer. A reviewer who sees the author's test suite will unconsciously adopt the author's framing of what matters. They will look for gaps in the existing tests rather than reasoning from first principles about what should be true. The reviewer's independent perspective is the entire point of having a reviewer. If we bias them from the start, then their perspective is compromised.

Stripping the tests forces the reviewer to think. It turns the review from a passive audit into an active exercise in understanding and challenging the code. And from our experience so far, the kinds of tests a truely independent reviewer will create end up being substantially different than the author's original tests—and they add much more value.

## Why an Educational PR Description?

Code does not communicate intent. Comments help, but they are local. A diff is usually what a reviewer reads, but a diff is just a red/green list of changes, not an explanation of a system or its intent. Asking a reviewer to reconstruct the author's mental model from a diff is asking them to solve an inverse problem: they have to reverse-engineer the intent from the code. That is EXTREMELY hard to do, and in practice, they don't! They pattern-match, skim, and then "LGTM", approved.

An educational PR description inverts the burden. The author, who already holds the mental model, takes responsibility for transmitting it. The reviewer can then spend their cognitive effort on the more valuable task: challenging that model rather than reverse-engineering it.

This also creates a useful accountability mechanism aroudn communication. If the reviewer consistently misunderstands the author's intent, the problem may be the author's communication, not the reviewer's competence.

The final result is a creative and intellectually engaging process for both the author and the reviewer. They really do end up being coauthors.

## The Role of AI

AI-assisted development is what makes this process both necessary and viable.

**Necessary** because AI enables developers to produce more code and more tests than a human reviewer can meaningfully evaluate using traditional line-by-line review. The bottleneck has shifted from code production to code comprehension and verification.

**Viable** because the reviewer can use AI to implement and amplify their own challenge. Once the reviewer has a mental model of the code, they can use AI to help enumerate edge cases, generate property-based tests, explore failure modes, and express formal properties. The reviewer's job is to direct this process with human judgment, expertise, and creativity. The AI helps with volume and thoroughness. AI can also make rigorous tools more accessible. Formal Methods is a powerful approch to validating software, but many of the tools require niche expertise to make use of them. AI can substantially reduce this burden so that powerful tools get used more often to gain high assurance of code quality.

The constraint is important: the reviewer uses AI to express and test their own mental model, not to summarize or evaluate the author's. The independence of the reviewer's perspective is what gives the process its value. If the reviewer outsources their side of the process to AI entirely, their independent creative perspective is lost and the value of their contribution evaporates.

## When to Use Dialectical Review

Dialectical Review is not appropriate for every PR. It is heavyweight by design. Use it when:

- The code is complex enough that a reviewer could plausibly misunderstand it.
- The code is important enough that undiscovered bugs have real consequences.
- The author and reviewer are both willing to invest the time and creative intellectual energy.
- You want to produce a high-quality, independently-motivated test suite as a side effect of the review process.

For trivial changes, config updates, or mechanical refactors, a standard review is fine. But you probably already knew this.

## Summary

1. Author submits code with an educational description. Tests are withheld.
2. Reviewer reads, learns, and builds their own independent mental model.
3. Reviewer writes adversarial tests from their own understanding, using any tools available.
4. Tests are run. Passes get merged. Failures start a conversation.
5. The codebase ends up with two independently-motivated test suites and a shared, verified understanding between author and reviewer.

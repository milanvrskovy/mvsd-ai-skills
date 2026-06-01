---
name: reasoning-trail
description: "Generate a concise, reviewer-optimised PR description by extracting decisions and reasoning from the current conversation. Trigger whenever the user says 'generate reasoning trail', 'generate PR summary', 'PR description', 'write up for code review', 'summarise this for a PR', 'generate review summary', or similar. Also trigger when the user asks to summarise design decisions, refactoring rationale, or implementation reasoning for peers or reviewers. Use this skill whenever a conversation contains code design, architecture discussion, or implementation work that will become a pull request."
---

# Reasoning Trail Generator

Generates a structured PR description from a conversation that contains design decisions, architecture discussion, refactoring rationale, or implementation work. The output is written for a reviewer who was not in the conversation and is reading the code cold.

## Core principle

**Try removing a line. If removing it does not harm the reviewer's understanding, the line was good to remove.**

Every sentence must earn its place. The summary is not a transcript of the conversation. It is the compressed reasoning trail: only the decisions that survived, the arguments that won, and the things the reviewer needs to verify. Iteration history, rejected tangents, and discovery steps that led nowhere are cut unless a rejected alternative clarifies why the chosen approach is better.

## Output format

Generate a single markdown document. Use exactly these sections in this order. Omit a section only where the rules below explicitly say to.

```markdown
# <Short imperative title describing the change>

## Context

<2-3 sentences. What problem is being solved. No assumed prior knowledge.>

## What changed

<Structural and/or behavioral summary. Describe the shape of the change, not a file-by-file diff. Prose for simple changes; short list for multi-part changes. Each item is a full sentence.>

## Why this approach

<The core reasoning. Why this design over alternatives. Fold rejected alternatives in here when they sharpen the argument -- "X was considered but rejected because Y" -- not as a separate catalogue. This section is the primary value of the summary; it captures what the diff cannot show.>

## Behavioural impact

<Breaking changes, behavioural equivalence, or net-new behaviour. For refactors: explicit equivalence claims with the specific evidence (e.g. "Pattern X guarantees Y, so the new extraction is equivalent"). For new features: what existing behaviour is affected, if any.>

## Review focus

<Where the reviewer should spend their time. Concrete: name the methods, files, edge cases, or open questions. If something was verified during the conversation but should be double-checked, say so.>

## Files changed

<One line per file: filename and what changed in it. Omit for PRs with 1-2 files where "What changed" already covers it.>
```

## Section rules

**Context** -- Always present. Never more than 3 sentences. Do not explain the codebase or framework to the reviewer; assume they know the project. Only provide enough to frame the problem.

**What changed** -- Always present. Describe structure, not mechanics. "Service now owns the full pipeline; domain only filters" is good. "Changed line 42 of ServiceClass.cls" is bad.

**Why this approach** -- Always present. This is the section that justifies the PR's existence. If the conversation contains no reasoning (pure mechanical change, dependency bump), write one sentence stating there is no design decision involved. If the change restructures a call chain or execution flow, include a concise before/after ASCII diagram. These communicate structural changes faster than prose.

**Behavioural impact** -- Include when any of these apply: refactor claiming no behaviour change (prove it), migration or data model change, API contract change, removal of functionality. Omit for net-new features with no legacy interaction, or when "What changed" already makes the impact self-evident.

**Review focus** -- Always present. Minimum one concrete item. Do not write generic advice ("please review carefully"). Name the thing.

**Files changed** -- Include when 3+ files are touched. Omit for small PRs where the other sections already identify the files.

## How to build the summary

1. **Scan the full conversation.** Identify: the problem, the approach taken, alternatives discussed, decisions made, things verified, things left open.

2. **Separate final decisions from iteration.** The conversation will contain false starts, corrections, and exploration. Only the final state matters. Include an earlier idea only if contrasting it with the final approach makes the reasoning clearer.

3. **Write Context first.** If you cannot state the problem in 3 sentences, you do not yet understand the conversation well enough. Re-read.

4. **Write Why before What.** The reasoning determines what is worth mentioning in the structural summary. If a change exists only to support a design decision, lead with the decision.

5. **Apply the removal test to every line.** Read the draft. Remove a sentence. If the summary still makes sense to a cold reviewer, leave it out. Repeat until every remaining line is load-bearing.

6. **Check for jargon leaks.** The conversation may use shorthand, nicknames, or context-dependent references ("the old way", "Elena's approach", "that hack"). Replace all of these with concrete descriptions.

## Conciseness rules

- No filler phrases: "It should be noted that", "As discussed", "After careful consideration".
- No meta-commentary: "This section describes...", "The following summarises...".
- No repeating information across sections. If Context states the problem, Why should not restate it.
- Bullet points are full sentences, not fragments. But keep them to one sentence each.
- If a section is one sentence, do not use a bullet. Write prose.
- Total length target: a reviewer should be able to read the entire summary in under 2 minutes. For a typical refactor or feature, this means roughly 200-400 words. A large, multi-concern PR may go longer, but question every paragraph.

## Quality checks

Before presenting the output, verify:

- [ ] A reviewer unfamiliar with the conversation can understand the PR from this summary alone
- [ ] No sentence survives the removal test (removing it would lose information)
- [ ] "Why this approach" contains at least one concrete reason, not just "cleaner" or "better"
- [ ] "Review focus" names specific methods, files, or edge cases
- [ ] No em dashes, no filler phrases, no "it should be noted"
- [ ] No information repeated between sections

## Output

Write the summary as a markdown file. Filename: `pr-summary-<short-descriptor>.md`.

Present the file to the user. Do not add commentary after presenting; the summary should speak for itself.

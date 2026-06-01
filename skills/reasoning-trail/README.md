# reasoning-trail

A Claude skill that generates PR descriptions from design conversations.

## The problem

Design conversations with AI produce valuable reasoning: why one approach was chosen over another, what alternatives were rejected, what edge cases were verified. None of this ends up in the PR description. Reviewers read the diff cold and reverse-engineer intent from code.

This skill extracts the reasoning trail from a conversation and compresses it into a structured, reviewer-optimised PR description.

## Output format

Every summary follows a fixed structure:

| Section | Purpose |
|---|---|
| **Context** | What problem is being solved, 2-3 sentences |
| **What changed** | Structural summary, not a file diff |
| **Why this approach** | Core reasoning, rejected alternatives folded in. Includes before/after flow diagrams when execution paths change |
| **Behavioural impact** | Equivalence proof for refactors, breaking changes for features. Omitted when self-evident |
| **Review focus** | Concrete methods, edge cases, and open questions for the reviewer |
| **Files changed** | One line per file. Omitted for small PRs |

## Core principle

> Try removing a line. If removing it does not harm the reviewer's understanding, the line was good to remove.

The output is not a conversation transcript. It is the compressed trail of decisions that survived, tuned for someone reading the code for the first time.

## Usage

Trigger phrases during or after a design conversation:

- "generate reasoning trail"
- "generate PR summary"
- "write up for code review"
- "summarise this for a PR"

The skill reads the conversation, extracts final decisions (not iteration history), and produces a markdown file.

## Installation

Download `reasoning-trail.skill` from [Releases](../../releases) and add it to your Claude skill library.

Alternatively, copy `SKILL.md` into your skills directory as `reasoning-trail/SKILL.md`.

## Scope

v1 generates PR descriptions only. The extraction pattern (compressing a conversation's decision history into a structured document) generalises to architecture decision records, handoff docs, and technical proposals. These may be added as output formats in future versions.

## License

MIT
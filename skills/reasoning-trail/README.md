# reasoning-trail

A skill for engineers who design features in conversation with AI and want to improve the peer review process by providing the reasoning behind their choices.

It reads the conversation, extracts the decisions that survived, and compresses them into a structured PR description — written for someone reading the code for the first time.

```
**Why this approach:** Chose path-based routing over regex matching because the
test suite already validates URL structures — adding regex would have introduced
a second parser with overlapping semantics. Rejected middleware chaining: too
much implicit ordering risk for a team that doesn't own the middleware stack.
```

That's it. The reasoning that survived the conversation, compressed for a reviewer reading the diff cold.

---

## Who it's for

- Engineers who prototype, design, or generate code with Claude
- Teams where reviewers frequently ask "why was this approach chosen over X?"
- Developers who write minimal PR descriptions and then spend review explaining decisions verbally
- Anyone who has had a thorough design conversation with AI and then written a three-line PR summary that captured none of it

---

## How it works

1. Work through a design problem with Claude — approaches, trade-offs, edge cases
2. Trigger the skill at any point during or after the conversation
3. The skill reads the conversation and extracts final decisions, not iteration history
4. It produces a structured markdown PR description, ready to paste

The output is not a conversation transcript. Alternatives that were rejected get folded into the **Why this approach** section. Execution path changes get a before/after flow diagram. Only what a reviewer needs survives.

---

## Commands

| Invocation | What happens |
|------------|-------------|
| `generate reasoning trail` | Produces a full PR description from the current conversation |
| `generate PR summary` | Same as above, explicit PR framing |
| `write up for code review` | Same, optimised for reviewer focus |
| `summarise this for a PR` | Lighter alias |

Aliases also work: `reasoning trail`, `PR writeup`, `code review summary`.

---

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

> Try removing a line. If removing it does not harm the reviewer's understanding, the line was good to remove.

---

## Installing

### Claude.ai (web/desktop)

1. Navigate to the skill folder in the repo - `skills/reasoning-trail/`
2. Open `SKILL.md` and download the raw file: **Raw → right-click → Save As**
3. In Claude.ai: **Settings → Customize → Skills → Upload**
4. Upload the `SKILL.md` file

### Claude Code

```bash
gh skill install mvsd/mvsd-ai-skills reasoning-trail --agent claude-code --scope user
```

Or manually:

```bash
mkdir -p ~/.claude/skills/reasoning-trail
curl -o ~/.claude/skills/reasoning-trail/SKILL.md \
  https://raw.githubusercontent.com/mvsd/mvsd-ai-skills/main/skills/reasoning-trail/SKILL.md
```

Restart your Claude Code session after installing.

---

## Compatibility

Built for Claude.ai and Claude Code. Uses the open SKILL.md standard and should work with other SKILL.md-compatible agents (OpenClaw, Codex CLI, etc.) though these are untested.

---

## License

MIT

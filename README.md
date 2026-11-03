# clarify-intent

An [Agent Skill](https://agentskills.io) that makes an AI agent **stop and ask
what you mean** instead of guessing, whenever a wrong guess would cost you
something.

## The problem

What you write as an instruction is rarely exactly what you mean. Agents fill
the gap with assumptions: they decide what "clean up" covers, which files are
"old", whether to fix a test or the code, or how far to expand the scope. You
often find out only when you review the result.

## What the skill does

When an instruction can be read in more than one way and the readings lead to
different results, the agent:

1. **Looks first**: reads the files and conversation so it doesn't ask what it
   could find out on its own.
2. **Restates your goal** in its own words, so a misunderstanding shows up
   right away.
3. **Asks a few specific questions**, each with concrete options, what each
   option leads to, and a recommendation.
4. **States the minor defaults** it will apply, so small assumptions are
   visible without turning each one into a question.
5. **Waits for your answer** before changing anything.

It also stops for decisions you didn't make: irreversible or destructive
actions, anything outward-facing, scope growth, instructions that contradict
themselves or their apparent goal, and vague words like "improve" or
"optimize" with no target.

It is meant to avoid **over-asking**. Trivial, easy-to-undo details get a
stated default instead of a question. If you say "don't ask, just do it", the
agent proceeds with the most conservative reading and lists its assumptions at
the **top** of its answer. Subagents return blocking questions to their caller
instead of guessing.

The full behavior is in
[`skills/clarify-intent/SKILL.md`](skills/clarify-intent/SKILL.md).

### Example

> **You:** Clean up this module.
>
> **Agent:** Before I start, I want to check that I understood you.
>
> My understanding: you want `utils.py` easier to read and maintain, without
> breaking its callers.
>
> 1. How far should "clean up" go?
>    a) Style only (imports, formatting, names): no behavior change ← recommended
>    b) Also fix the unclosed file handles in `load`/`save`: small behavior change
>    c) Also remove `old_fmt`: the comment says the billing export still uses it
>
> Unless you say otherwise I'll also keep the public function names unchanged.
>
> I'll wait for your answer before changing anything.

## Installation

### Claude Code: as a plugin (recommended)

This repository is both a plugin and a single-plugin marketplace:

```sh
claude plugin marketplace add franks-ai-skills/clarify-intent
claude plugin install clarify-intent@clarify-intent
```

Or inside a Claude Code session:

```
/plugin marketplace add franks-ai-skills/clarify-intent
/plugin install clarify-intent@clarify-intent
```

To try a local checkout without installing it:

```sh
claude --plugin-dir /path/to/clarify-intent
```

### Claude Code: as a plain skill

Copy the skill folder into your personal or project skills directory:

```sh
# for all your projects
cp -R skills/clarify-intent ~/.claude/skills/

# for one project only (commit it to share it with your team)
cp -R skills/clarify-intent /path/to/project/.claude/skills/
```

### Other agents

`SKILL.md` follows the open [Agent Skills](https://agentskills.io) format
(YAML front matter with `name` and `description`, then Markdown
instructions). Agents that support that format can load the
`skills/clarify-intent/` folder from their skills directory; see your agent's
documentation for where that is. For agents without skill support, paste the
body of `SKILL.md` (everything after the front matter) into the agent's system
prompt or rules file, for example `AGENTS.md`.

## Usage

The skill loads **automatically** when the agent judges a request ambiguous.
You can also call it explicitly:

| Installed as | Command |
|--------------|---------|
| Plugin | `/clarify-intent:clarify-intent` |
| Plain skill | `/clarify-intent` |

Saying "ask me first", "don't assume", or "check with me" also triggers it.

**Making it reliable.** Automatic loading depends on the model matching your
request to the skill's description, and models tend to underestimate how
ambiguous an instruction is. If you want the behavior on every task, add a
line like this to your `CLAUDE.md` (or `AGENTS.md`):

```markdown
Before acting on an ambiguous or underspecified instruction, follow the
clarify-intent skill: ask about my intent instead of assuming.
```

## Testing

```sh
# check manifests and skill front matter
claude plugin validate .

# run the behavioral evals (with and without the skill, for comparison)
claude plugin eval . --no-publish
```

The cases in [`evals/`](evals/) cover both directions:

| Case | Expected behavior |
|------|-------------------|
| `ambiguous-cleanup` | asks what "clean up" should cover (automatic triggering) |
| `explicit-cleanup` | same request with `/clarify-intent`: asks before rewriting |
| `destructive-undefined` | asks what "old" means before deleting anything |
| `literal-vs-goal` | flags a test that looks wrong instead of silently "fixing" it |
| `clear-rename` | does **not** ask; just does the fully specified task |
| `no-questions-waiver` | does **not** ask when told not to; states assumptions instead |

Each eval run starts real agent sessions on your account, so it costs tokens.
Add `--runs 1` for a cheaper smoke test. Results are written to
`evals/results/`, which is git-ignored.

## Known limitations

- **Automatic triggering is not guaranteed.** The agent decides from the skill's
  description whether to load it, and it tends to skip the skill when it
  feels confident about a low-stakes request. In the `ambiguous-cleanup` eval
  (a "clean up" request about a code snippet in chat) the skill fired in 3 of 5
  runs during development; with explicit invocation, it asked every time. For
  destructive requests such as `destructive-undefined` it fired reliably. If
  you want the behavior every time, add the `CLAUDE.md` line shown under
  [Usage](#usage) or invoke the skill explicitly.
- **It changes behavior, not permissions.** The skill is instructions only.
  It cannot technically stop an agent from acting; use your agent's
  permission settings for that.

## Contributing

Issues and pull requests are welcome, especially new eval cases where the
agent guessed when it should have asked, or asked when it shouldn't have.
When you change `SKILL.md`, run the evals before and after.

## License

Licensed under the GNU Affero General Public License v3.0 only
([AGPL-3.0-only](LICENSE)).

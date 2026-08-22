# research-agent-skills
A repository for the skills required for my research activities (primarily code development) involving AI agents

## Skills

| Skill | Purpose | Agents |
| --- | --- | --- |
| [`teach-back`](skills/teach-back) | Evidence-based teach-back on the user's own understanding | Claude Code, Codex |
| [`teach-back-quiz`](skills/teach-back-quiz) | Optional self-study quiz from a user-authored note | Claude Code, Codex |
| [`blueprint-first`](skills/blueprint-first) | Require a user-authored design before implementation | Codex |

## Installation

Each skill directory is self-contained. Link the directories you want into the
skill directory your agent reads, from the root of a local clone.

### Claude Code

```sh
mkdir -p ~/.claude/skills
ln -sfn "$PWD/skills/teach-back" ~/.claude/skills/teach-back
```

Invoke a linked skill with `/teach-back`. Use `<project>/.claude/skills/`
instead to scope a skill to one project. Claude Code uses only `SKILL.md` for
discovery and does not read `agents/` or `README.md`.

### OpenAI Codex

```sh
mkdir -p ~/.agents/skills
ln -sfn "$PWD/skills/teach-back" ~/.agents/skills/teach-back
```

Invoke a linked skill with `$teach-back`. `agents/openai.yaml` supplies the
Codex interface metadata and the implicit invocation policy. Registering the
directory in `~/.codex/config.toml` under `[[skills.config]]` is an
alternative to linking.

Prefer a symlink over a copy so a linked skill tracks this repository. Start a
new agent session after linking so the skill is discovered.

`blueprint-first` is written for Codex: its `SKILL.md` addresses the agent as
Codex and has not been ported. It additionally expects the Superpowers skills
`using-git-worktrees` and `writing-plans` to be available; see
[skills/blueprint-first/README.md](skills/blueprint-first/README.md).

## AI assistance

The skills in this repository were developed with assistance from
OpenAI Codex and Claude Code. The maintainer made the final decisions
regarding their concepts, workflow boundaries, revisions, and published
contents.

## License

MIT License. See [LICENSE](LICENSE).

# Skills for clearer agent work

Portable [Agent Skills](https://agentskills.io/) by Jari Mustonen: instructions
for improving instructions, recovering the meaning of documents, and preserving
what people actually said. These are Markdown workflows, not a CLI or a model.

## Skills

- [rethink-instructions](skills/rethink-instructions/SKILL.md) — replace procedural control with purpose, context, and reasons.
- [rethink-document](skills/rethink-document/SKILL.md) — separate actual decisions from detail an agent invented, then rewrite with the user.
- [fact-check-instructions](skills/fact-check-instructions/SKILL.md) — check tool, path, and workflow claims against current evidence.
- [llm-skill-review](skills/llm-skill-review/SKILL.md) — review agent-directed prose through focused lenses and triage concrete findings.
- [meeting-transcript](skills/meeting-transcript/SKILL.md) — preserve the raw transcript and write a readable, speaker-attributed account in the appropriate language.

## Install and use

Use a harness that supports Agent Skills and directory symlinks. Clone this
repository somewhere permanent, then link the skills into its user skill directory.
For example, in Bash on Linux or macOS, to install all five in Claude Code:

```bash
git clone https://github.com/jarimustonen/skills.git "$HOME/agent-skills"
mkdir -p "$HOME/.claude/skills"
for source in "$HOME/agent-skills/skills/"*/; do
  name=$(basename "$source")
  destination="$HOME/.claude/skills/$name"
  if [ -e "$destination" ] || [ -L "$destination" ]; then
    printf 'Already exists; inspect before replacing: %s\n' "$destination" >&2
  else
    ln -s "${source%/}" "$destination"
  fi
done
```

For pi, use `~/.pi/agent/skills` instead of `~/.claude/skills`. For Codex, use
`${CODEX_HOME:-$HOME/.codex}/skills`. Respect a custom configuration directory
if your harness uses one. Link whole skill directories, not just `SKILL.md`.
Restart or reload the harness after installation and confirm the skills appear
in its catalog. Descriptions support automatic discovery; where slash invocation
is supported, try `/rethink-instructions path/to/AGENTS.md` or
`/meeting-transcript path/to/transcript.txt`.

Existing installations are left untouched. Remove only links you created to
uninstall. Update with `git -C "$HOME/agent-skills" pull --ff-only`; review
changes first if reproducibility matters, or check out a specific commit.

## Requirements and boundaries

The agent needs file-reading and editing tools. Git is useful for history and
focused commits, but the workflows can also be used outside version control;
report edits instead of making a commit there. No scripts or companion skills
are required. `rethink-instructions` and `rethink-document` serve different
purposes and can be installed independently.

`llm-skill-review` additionally needs an available, authenticated model-consultation
interface. It does not install one or assume a particular provider. It reports a
blocked consultation rather than claiming an independent review occurred. Check
permission before sending private material to an external provider.

`meeting-transcript` processes an existing text transcript; it does not transcribe
audio. It asks about uncertain speaker identities, follows the destination’s
privacy conventions, and retains the source unless cleanup is authorized. No
meeting material is included in this repository.

## Source and contributions

This public repository is the authoritative source for these five skills.
Consumers may pin it as a submodule or link a checkout; copies will not receive
updates automatically. Each skill lives at `skills/<name>/SKILL.md`. Changes
belong here, not in an installed copy. Issues and pull requests are welcome;
include the instruction or a sanitized example and the behavior it caused.
Never include private prompts, transcripts, or identifying meeting details.

MIT licensed; see [LICENSE](LICENSE).

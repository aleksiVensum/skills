# Skills for clearer agent work

[Agent Skills](https://agentskills.io/) by Jari Mustonen for improving agent
instructions, clarifying documents, and turning meeting transcripts into
readable accounts, plus a UI prototyping skill adapted from Emil Kowalski's
collection.

## Install

```bash
npx skills@latest add jarimustonen/skills
```

In [pi](https://pi.dev), the repository also works as a package:
`pi install git:github.com/jarimustonen/skills`.

## Skills

- [rethink-instructions](skills/rethink-instructions/SKILL.md) — replace procedural control with purpose, context, and reasons.
- [rethink-document](skills/rethink-document/SKILL.md) — separate actual decisions from detail an agent invented, then rewrite with the user.
- [fact-check-instructions](skills/fact-check-instructions/SKILL.md) — check tool, path, and workflow claims against current evidence.
- [llm-skill-review](skills/llm-skill-review/SKILL.md) — review agent-directed prose through focused lenses and triage concrete findings.
- [meeting-transcript](skills/meeting-transcript/SKILL.md) — preserve the raw transcript and write a readable, speaker-attributed account.
- [prototype](skills/prototype/SKILL.md) — build several genuinely different versions of a UI piece behind a live picker. Adapted from [emilkowalski/skills](https://github.com/emilkowalski/skills); its own [MIT license](skills/prototype/LICENSE) keeps the original copyright.

[MIT License](LICENSE), except `skills/prototype`, which carries its own MIT
license and copyright.

---
name: llm-skill-review
description: Review a SKILL.md or other agent-instruction file as prose that future agents will execute. Runs skill-specific review lenses (executor ambiguity, trigger fit, edge cases, cross-skill contracts, blast radius, script extraction, design fidelity) through one economical model, then triages the findings inline for the user. Use when asked to review, check, or stress-test a skill or agent instruction. Not for code, diffs, or plans, and not for rewriting an instruction (rethink-instructions).
---

# Skill review

## What this is for

A skill file is prose that future agents execute without the author present.
A gap in it is repeated by every agent that loads it, and a skill used by
parallel workers repeats it at scale. This review reads the
skill the way those agents will and finds the places where they would have to
guess, would be routed to the wrong skill, would break a callee's contract, or
could do damage. The output is a short triaged list that the user can act on
directly, without a separate code-review assessment round. Skill findings need the axes below.

The target is a `SKILL.md` in your harness’s skill catalog or its source, but
any file of agent-directed prose qualifies: an `AGENTS.md`, worker prompt, or
template. If the path is not agent-directed prose, explain the mismatch. A typo
alone is not worth a review round.

## Review effort and prerequisites

Use one available external model over the two lenses most likely to matter.
Model agreement is not evidence; check findings against the actual instructions.
For a security or destructive boundary, or a workflow used at large scale, a
second distinct model and additional lenses may earn their cost. Explain why.
There is no mode with more than two models.

This skill needs an authenticated model-consultation interface capable of
receiving prompts and attached text. Use the host environment’s supported tool
or CLI and its documented syntax; no particular provider or companion skill is
required. Choose a model available there, not a hard-coded model identifier.
If consultation is unavailable, report the blocked review rather than pretending
a local reading is an independent review. Do not send confidential instructions
to an external provider without permission.

## Arguments

`$ARGUMENTS`

- `--models <list>`: model IDs supported by the consultation interface.
  Routine rigor takes one model;
  critical takes at most two distinct ones.
- `--lenses <list>`: subset of `executor,trigger-fit,edge-case,cross-skill,
  blast-radius,script-extraction,design-fidelity`. Default is adaptive:
  `executor,cross-skill` when the skill invokes other skills, `executor,
  edge-case` otherwise, and `executor,design-fidelity` when a design document
  is available. A wider explicit set belongs with `--rigor critical`.
- `--rigor <routine|critical>`: default `routine`. See above.
- `--include-callees`: on by default. Read the skills the target invokes and
  attach them for the cross-skill lens.
- `--design-doc <path>`: a design-intent document to compare against. Without
  it, a `design.md` beside the target is used if present.
- `--dry-run`: run the lenses, print raw output grouped by lens, stop.
- `--log-prompts`: save prompts and raw outputs locally only when requested.
  Treat these logs as potentially sensitive and report their location.

What remains after the flags is the path to the target.

## Gathering context

Read the target. Then find its callees: any `/<name>` reference or "run
`/<name>`" instruction names a skill, whatever harness the target was written
for. Look for each callee's `SKILL.md` in the current harness's loaded catalog
first, then `~/.claude/skills`, `~/.pi/agent/skills`,
`${CODEX_HOME:-~/.codex}/skills`, and finally the project’s authored skill sources. Their bodies
are what the cross-skill lens compares against, so a callee you could not find
is worth mentioning in the report rather than silently omitting.

Before spending any calls, tell the user the target, the callees found, the design
document or none, the lenses and models, and the call count. That is the moment
he can redirect cheaply.

## The lenses

Each lens is one prompt, sent with the full skill body attached. Use the same
lens set for every model in a run so that their outputs are comparable. The
reviewer has none of the repository loaded; it sees only what you attach.
Findings that depend on a repository convention the skill does not mention will
be missed or invented, which is itself a useful signal that the skill should
state the convention.

**Executor.**

> Read this skill as if you are the agent about to execute it. Walk through
> every step. List every place where you would have to guess, ask the user a
> question the skill does not anticipate, or make a judgment call the skill
> gives you no criteria for. For each, quote the exact phrase and explain what
> is ambiguous.

**Trigger-fit.**

> Using only the skill's `name` and `description`, write (a) five user prompts
> that should route to this skill and (b) five plausible prompts that should
> not, but might if the description misleads. Then read the body. For each
> prompt, say whether the skill actually handles it well, and flag every
> mismatch between what the description promises and what the body does.

**Edge-case.**

> Enumerate the failure and edge cases this skill should cover: empty or
> malformed input, dirty repository state, missing prerequisites, interruption
> mid-run, concurrent invocation, partial completion, resource exhaustion such
> as disk or API rate limits, and prerequisites that are unauthorized or
> destructive. For each, say whether the skill addresses it, addresses it
> weakly, or ignores it.

**Cross-skill.** Attach the callee bodies.

> Identify every other skill this one explicitly invokes. For each, read the
> called skill's contract in the attached context: its arguments, required
> preconditions, outputs, and side effects. List every mismatch: a missing
> precondition, a wrong argument shape, an assumption about output the callee
> does not guarantee, or an instruction that contradicts the callee's own rules.

**Blast-radius.**

> List every action this skill could take that is irreversible or affects
> shared state outside the working directory: deletes, force-pushes, remote
> API writes, mass file operations, spawning many sub-processes, token spend at
> scale, modifying upstream branches. For each, say whether the skill requires
> confirmation, offers a dry run, has a rollback path, or proceeds blindly.
> Describe the worst case if it were run on a wrong input.

**Script-extraction.** This lens exists because agents reliably follow a
one-line shell call but skip multi-step prose procedures once the main task
feels done, especially late in a workflow. The prompt states that premise
because the reviewer needs it.

> Premise: skill files are agent-readable prose. Agents reliably follow
> one-line shell calls but skip multi-step prose procedures when the main task
> is done. So a step that is mechanical (deterministic, no judgment) should
> usually be a script the skill invokes, not prose the agent executes.
> Conversely, a skill sometimes scripts something that is judgment-heavy and
> would be better as prose.
>
> Identify candidates in both directions.
>
> 1. EXTRACT-script: any step where the skill asks the agent to perform a
>    multi-step mechanical procedure that would be more reliable as one script
>    invocation. Look especially for path derivation with collision handling
>    and structured file writing; state-machine bookkeeping (counters,
>    manifests, sequence IDs); schema enforcement or validation; parsing
>    structured output; atomic file moves; sequenced cleanup of three or more
>    ordered operations; looping over N items with bookkeeping. For each, quote
>    the prose, propose a concrete `scripts/<name>.sh <args>` signature, and
>    rate the agent-skip risk: high (late-workflow, precision required,
>    competes with user-facing work, or repeated many times per run), medium
>    (mid-workflow, partly mechanical, or a one-shot step whose skip is
>    recoverable), or low (early, single-step, judgment-tied).
> 2. INLINE-prose: any step already externalized to a script that is
>    judgment-heavy enough that a script will get it wrong, such as scripts
>    taking freeform input, ranking, or branching on semantic content. For
>    each, quote the script call, explain why judgment does not fit a script,
>    and sketch the prose alternative.
>
> Where a candidate overlaps with executor ambiguity, edge-case neglect, or
> blast radius, say so; one extraction often resolves several concerns. Be
> concrete: "lines X to Y compute a run id, check collisions, and write
> markdown; that is a log-run script" is useful, "could be scripted" is not.
>
> Output format:
>
> ```
> ## EXTRACT-script
> - title: <short>
>   quote: "<verbatim from skill>"
>   proposed_script: <path and brief signature>
>   agent_skip_risk: high|medium|low
>   cross_lens: <other lenses also resolved, or '-'>
>   rationale: <one sentence>
>
> ## INLINE-prose
> - title: <short>
>   quote_or_script_ref: "<verbatim or script name>"
>   why_script_fails: <one sentence>
>   prose_alternative: <one sentence>
>
> ## Net assessment
> <two or three sentences on the prose/script balance and the single
> highest-value change>
> ```

**Design-fidelity.** Only when a design document exists; attach it.

> Read the attached design-intent document and the skill side by side. List
> every place where the skill drifts from the design: scope creep, under-
> scoping, contradiction of something the design ruled out, neglect of a
> failure mode the design said must be handled, a different level of
> autonomy than the design specified, or different handling of user feedback.
> For each finding quote both the design statement and the conflicting skill
> phrase. If a drift looks like a legitimate refinement discovered while
> writing, label it INTENTIONAL DRIFT rather than a violation; it needs human
> acknowledgement, not silent correction.

## Running the lenses

Use the consultation interface’s documented attachment and timeout support.
Issue one call per model and lens, attaching the skill body, callee bodies for
cross-skill, and design document for design-fidelity. Keep raw responses indexed
by model and lens for triage. If a call fails, report which part is incomplete;
do not silently substitute another workflow or claim the review completed.

With `--dry-run`, print the raw responses grouped by lens and stop.

## Triaging the findings

Collapse duplicates first. The same defect often appears under two lenses, or
from both models in a critical run; note how many places raised it, but treat
that as a hint about conviction, not as evidence. A lens that quotes the skill
is the evidence.

Judge each finding on four things, because they are what decides whether a
fix helps future agents:

- Concreteness: quoted from the skill, paraphrased, or a generic worry.
- Operational impact: would an agent hitting this gap silently do the wrong
  thing, fail loudly and recoverably, or merely look untidy?
- Reader cost: would the fix make the skill shorter, leave it as it is, or pad
  it with caveats that every future reader has to wade through?
- Convergence: raised once, by both models, or under several lenses.

From those, decide. A silent-wrong finding is a FIX, as is a loud failure that
is quoted and whose fix does not lengthen the skill, when the operational impact is supported by the text. Agreement between
models alone does not justify a FIX. A finding with real impact whose fix would add noise, or
that turns on a trade-off the user should settle, is DISCUSS. A cosmetic finding
whose fix adds text, or an ungrounded concern from a reviewer, is SKIP. Present a
single table grouped by decision, each row giving a short title, the lens, the
axes, the quote where there is one, and a one-line recommendation.

Design-fidelity findings are reconciliation choices between the draft and the
design, not edits to skill text, so give them their own table with their own
outcomes: FIX-vs-design when the skill drifted without justification,
UPDATE-design when the draft found a better path and the design should follow,
DISCUSS-drift when neither side is obviously right, and INTENTIONAL when the
skill already acknowledges the difference. Quote both sides in each row.

Script-extraction findings are also their own table, because acting on them
means writing or removing a script rather than editing prose: EXTRACT-script
for a mechanical step with skip risk of medium or more and modest script
complexity, INLINE-prose for a script doing judgment work, DISCUSS-extraction
for a borderline mechanization win against maintenance cost, and KEEP when the
split is right as it is. Script authorship is a task of its own; an autonomous
worker running this review recommends it rather than implementing it.

## Presenting and applying

Show the tables. For each FIX, offer the concrete edit; for DISCUSS, state the
trade-off in a sentence or two; for SKIP, give the one-line reason so the user can
object. Apply edits only when the user picks them. A skill edit is cheap to make and
expensive to be wrong about, because it changes how every later agent behaves
and the effect surfaces far from the edit, so this is one of the places where
their choice is worth the interruption.

## Keeping this skill honest

Cross-skill is especially useful for workflow chains: contract drift between
links often causes failures far from the edited file. When this skill changes
materially, review it against its own lenses. Do not recursively launch reviews;
the goal is a checked instruction, not an unbounded consultation loop.

## Example invocations

```
/llm-skill-review path/to/my-skill/SKILL.md
/llm-skill-review --lenses executor,cross-skill path/to/my-skill/SKILL.md
/llm-skill-review --rigor critical --lenses cross-skill,blast-radius path/to/my-skill/SKILL.md
/llm-skill-review --design-doc docs/design.md path/to/my-skill/SKILL.md
```

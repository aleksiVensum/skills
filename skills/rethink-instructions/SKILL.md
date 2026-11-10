---
name: rethink-instructions
description: Review a skill, AGENTS.md, worker prompt, implementation issue, or other agent instruction and rewrite it so that it relies on the agent's understanding instead of prescribed steps, gates, and prohibitions. Use when asked to rethink, simplify, or de-proceduralize an agent instruction, or to judge whether one trusts its reader enough.
---

# Rethink agent instructions

## What this is for

Instructions written by AI tend to grow into procedure manuals: numbered
steps, gates, mandatory checks, capitalized prohibitions, output templates. They
treat the reader as something that must be fenced in.

The reader is a capable agent. It is sensible, consistent, able to decide for
itself, and inclined to avoid doing stupid things. An instruction written for
that reader looks different. It says what the work is for, supplies what the
agent could not know, offers a way of thinking about the task, and points out
what deserves attention. Then it leaves the work to the agent.

Your task is to take an existing instruction and produce the version that reader
deserves, together with a short account of what you changed and why.

## Why procedure fails

A list of steps works in the situations its author imagined. Elsewhere the agent
follows the letter while the purpose goes unserved, because the purpose was never
written down. Procedure also crowds out the content. In a long instruction the
three sentences that matter are hard to find among the thirty that any competent
agent would have done anyway.

An agent that knows what is being achieved and why can handle the case nobody
anticipated. That is the whole argument for writing this way.

## What a good instruction contains

**The purpose.** What is being achieved, for whom, and what a good result looks
like.

**Knowledge the agent lacks.** Facts about this environment, earlier decisions
and their reasons, and traps that the available sources do not reveal. Where the
knowledge already lives somewhere, such as a command's help text, the source
code, or a separate document describing a framework or method, the instruction
says where to find it and does not copy it. Copies go stale and the source does
not.

**An approach.** How to think about this kind of work: what to favour when goals
pull in different directions, what good practitioners pay attention to.

**Things to keep in mind.** Considerations with their reasons. These are offered
to the agent's judgment. They are not orders.

## Telling knowledge from control

The useful question about any sentence is whether it tells the agent something it
could not have known, or tells it how to behave. "This command exits zero even
when it fails" is knowledge. "Step 3: verify that the command succeeded" is
control, and once the agent has the knowledge the step is unnecessary.

Much control is knowledge in disguise. Behind most rules there was once a reason:
something broke, someone was annoyed, something was lost. Recover the reason and
write that down. A prohibition with its reason is something the agent can apply
intelligently, including in cases the rule did not cover. If no reason can be
found and none can be imagined, the rule was probably noise.

The opposite failure is vagueness. "Use good judgment" gives judgment nothing to
work with. Understanding is concrete: reasons, consequences, what is at stake,
what good looks like. If your rewrite is shorter but the agent would know less
about the situation than before, you have removed the wrong things.

Some precision is legitimate and stays. An interface that another program or
agent depends on has to be exact. An order of operations that matters for a
non-obvious reason is worth stating with that reason. A few things are dangerous
enough to state as plain limits. These should be rare, and each should say what
would be lost.

## Reasons that come back as output

When the instruction governs a text the agent will write, such as a report, a
summary, or a message to a customer, the reasons you add are read by the same
agent that writes that text, and a reason phrased like a finding can come back
as one. An instruction for a status report said that the steering group wants
to know whether the migration is on track. The next draft opened with "The
migration is on track", although its own dates showed a slip. The agent had
been handed a well-formed sentence about the subject and used it.

Putting the reason as a question did not prevent this, because dropping
"whether" leaves the claim intact. What helps is to write the reason about the
reader and what they do with the text, in words that could not stand in the
text itself: "the steering group decides from this section whether to release
the next phase, so it needs the current date against the planned one." That
tells the agent what to find out and leaves it nothing to repeat.

A quick test is to read each reason as if it were a sentence in the output.
If it would pass there once a word or two is dropped, reword it.

## How instructions should treat autonomy

Procedural instructions usually handle autonomy with permission rules: confirm
before anything destructive, ask when unsure. These rules reason from the
category of an action, and the category is a poor guide. They produce agents that
ask permission to wipe a test server and want approval for a branch name.

What matters is what is at stake. Interrupting the user has a cost, because their
attention is the scarcest resource in the system. A question is worth asking when
the possible outcomes differ in a way the user would care about and the agent
cannot tell which they would choose. If any reasonable choice would do, the agent
makes it, says what it chose, and continues. Whether an action can be undone
matters only in proportion to what would be lost. Deleting something disposable
needs no permission. A small change to something precious may.

An instruction therefore serves autonomy best by describing the stakes: what in
this environment is valuable, what is disposable, and what the user has opinions
about. When you find permission gates, replace them with that description. If the
original does not reveal the stakes, look for them in the surroundings before you
consider asking.

## Doing the work

Read the instruction until you understand what it is trying to achieve. If it
references tools or documents, look at them, because you need to know whether the
instruction repeats what they already say. Then write the instruction you would
want to receive if the task were yours.

Expect the result to be much shorter. Prefer prose to lists when the ideas depend
on each other. Avoid emphasis by capitals and words like "must" and "never"
outside the rare real limit. They signal distrust, and a reader who meets them
everywhere learns to ignore them.

The frontmatter description of a skill has a different job. It helps a harness
decide when to load the skill, so judge it by whether it routes well.

Find out where the instruction is authored before you edit. An installed copy
may be overwritten by the tool that owns it, in which case the change belongs in
that tool's source. Where the file is under version control, edit it in place,
since the history keeps the original. Make the rewrite its own commit, and say
in its message what the original was protecting and what you dropped, so that
whoever later misses a rule can find out why it went.

Alongside the rewrite, tell the user briefly what the original was trying to
protect, what you kept, and what you dropped. Mention anything you removed whose
purpose you could not determine but which might have guarded something valuable.
That is the one place where the user's knowledge is likely to be needed.

## Carrying these principles into the instruction

The reasoning in this document is available to you as material. When the
instruction you are rewriting would benefit from one of these ideas, write it
into the instruction in words that fit the task. An instruction for work that
runs long and unattended, for example, may need to say that interrupting the
user has a cost and how to weigh it, because the agent reading that instruction
will not have read this one. Add such a passage where the task calls for it and
leave it out where the agent would behave well without it.

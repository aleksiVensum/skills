---
name: fact-check-instructions
description: Check what a skill, AGENTS.md, worker prompt, or other agent instruction claims about tools, files, and other instructions against how those things are now, and repair what has gone stale. Use when asked to fact-check or verify an agent instruction, or when an instruction is suspected of describing tools as they used to be.
---

# Fact-check agent instructions

## What this is for

An instruction tells an agent about the world it will work in: which commands
exist and what they accept, what a tool cannot do, where files live, what
another skill expects. Those statements were true when they were written. The
tools have kept changing since, and nobody updates the instruction when they
do. Old instructions therefore describe workarounds for limitations that no
longer exist, features "coming soon" that shipped months ago, and paths that
have moved.

An agent has no reason to doubt its instructions, so it acts on all of this. It
takes the detour, avoids the working feature, and looks in the old place. Your
task is to find where an instruction and the world have drifted apart, so that
the instruction can be trusted again.

This is a different job from improving how the instruction is written. Leave
its style, structure, and length as they are. A reader comparing the file before
and after should see only facts that changed.

## What to look at

Read the instruction for anything that could be true or false. Statements about
tools are the obvious ones. Less obvious are the claims hidden inside advice: a
warning implies that the danger still exists, and a workaround implies that the
direct route is still closed.

Check each claim where the truth lives: the tool's help text, its source, the
documents and skills the instruction refers to, the repository's history and
issues. What the agent will meet is the version installed on the machines where
the instruction runs. A newer state in a source checkout is worth mentioning to
the user, but the instruction should describe what is installed.

Look also at whether the instruction agrees with itself and with its
neighbours. One part may forbid what another part requires. An early exit may
skip something a later part depends on. Another skill may call this one and rely
on behaviour it no longer has. These are as misleading to an agent as a wrong
flag name.

You will not be able to check everything, and not every claim matters equally.
A wrong claim about something the agent does on every run, or about something
that can lose data, deserves the most care. A detail in a rarely used corner
deserves less.

Checking means reading and asking. Help output, source, listings, and dry runs
tell you what you need. The operations the instruction describes, such as
sending mail, deploying, or archiving, stay undone.

## What to do with what you find

When a claim is plainly out of date and the correct fact is clear, change it in
the instruction, touching as little text as possible. Make the change where the
instruction is authored. An installed copy may be overwritten by the tool that
owns it, and then the repair belongs in that tool's source.

Some findings are not yours to settle. A contradiction between two rules usually
means the user decided something twice, and only they know which decision
stands. A workaround that is no longer needed may have shaped a habit the user
likes. When repairing a fact would change what the agent ends up doing in a way
the user might care about, describe what you found and what you would do, and
leave the text alone.

The instruction is read by agents who will never see your report. Keep remarks
about what the text used to say, and about what is awaiting a decision, out of
the instruction itself.

Tell the user what you corrected and what evidence showed it, what you found
that needs their decision, and what you could not check. The last part matters
as much as the first two, because it tells them which claims still rest on the
instruction's word alone. If you explain why something in the instruction
behaves oddly and the explanation is your own inference, say that it is one.

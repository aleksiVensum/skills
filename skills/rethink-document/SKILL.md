---
name: rethink-document
description: Rewrite an AI-written ADR, design or architecture document, or issue text so that a person can read it, by separating what was actually decided from what an agent added while writing, and agreeing that separation with the user before rewriting. Use when asked to rethink, clarify, or make readable an ADR, design document, or issue, or when such a text is dense, defensive, and hard to follow. Not for skills, AGENTS.md, worker prompts, or implementation issues whose problem is procedural control (rethink-instructions), for prose style alone, or for filing a new issue.
---

# Rethink a document

## What this is for

Decision records, design documents, and issues written by agents become dense
and defensive. The reader cannot find what was decided, why, or which of the
fifty stated constraints anyone cares about. A person wants to understand the
decision in a few minutes, and an agent implementing from the text will treat
everything in it as binding. Both need a document that says what was decided
and why, and little else.

You produce that document together with the user, because which statements
are real is partly known only to them.

## How these documents get this way

A person gives short feedback or approves a recommendation in a sentence. The
agent writing it up designs the solution, picks numbers, and forbids the
misreadings it can imagine. Reviews add a sentence per finding. The next
document is written from this one and inherits all of it as approved. At each
step invented detail acquires the authority of the original decision, and an
invented requirement that cannot be met will stop the work that depends on it.

## Where a statement comes from

Sort the statements by origin.

**What a person decided or observed** stays. The evidence is outside the
document: decision entries, feedback as first recorded, earlier decisions. A
line saying someone approved the design does not make every sentence under it
approved. A person's words are the first recorded version; what an agent
later edited into them is body text.

**What follows from the decision** need not be written once the principle is
stated with its reason.

**The writing agent's proposals**, such as a timeout value or a field name,
may be good but are not decisions. One that is worth keeping becomes a
sentence saying what is proposed and where the detail lives.

**Steam** goes: hedges, denials of things nobody suggested, restatements, the
record of what the document itself did not do.

The sources are wider than what the document cites. The approval may be in
another issue, the file's git history shows who wrote which passage and when,
and code and later documents show whether a statement is still true. Sorting
from the document alone is a guess, so say so if that is all you have.

## Contradictions

Sorting turns up places where the document disagrees with something decided
later. These matter more than the prose, because an agent will follow the
stale text. Tell the user about each one, and look for its copies in the
documents written from this one.

In an issue, correct the body to match the later decision. A decision record
is different. Narrowing it to what the person decided is restating it.
Replacing their decision with a later one is not, so report that contradiction
and leave the change to the repository's way of superseding a decision.

## Agreeing the sorting with the user

Do the sorting yourself first. Then show the user, once and before rewriting,
in the language they are using:

- the core of the document in a few sentences
- the contradictions you found
- where the rewrite will commit to less than the original appeared to
- the questions only they can answer

This should take a minute to read. A question is worth asking when the answer
changes what the document commits to: whether a limit was theirs, whether a
later decision cancelled an earlier requirement. Everything else you decide.

## What the rewritten document looks like

It opens with the problem as someone experienced it, then states the decision
and its leading idea, with reasons beside the choices they explain. After
reading the decision, someone should be able to say what was decided without
having learned the design.

A document does one job. An interface belongs in the specification, a test
plan in the implementing issue, a finding or blocker where the repository
records those. Find the place that exists, or propose a new one as new. The
document gets the corrected statement, not the story of the correction.

A decision record should come out at half its length or less. An issue keeps
its decision entries and the person's words verbatim, so it becomes clearer
rather than shorter: the body agrees with the decisions, and acceptance
describes an outcome the person would recognise, not a mechanism. Use the repository’s issue tooling and preserve its decision history when editing one.

## Finishing

Make the rewrite its own commit. Its message says what the document now
commits to, what moved and where, and on whose word anything was narrowed,
because whoever next sorts this document will read that history. Tell the user
the same briefly, and anything you dropped whose purpose you could not
determine.

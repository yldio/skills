---
name: rfc-writing
description: Write, draft, or review RFCs and architecture decision documents the YLD way — decision-first, dense, senior-to-senior. The RFC is a living document, updated as implementation lands, not a snapshot of intent. Use when asked to write, create, draft, review, or update an RFC, ADR, technical proposal, design doc, or decision record, or when a feature/architecture decision needs documenting.
---

# RFC Writing — the YLD way

How YLD engineers write RFCs and architecture decision documents. The style
serves one goal: **a decision document a senior engineer can read cold, agree
or disagree with, and act on** — in the client's repo, long after the
engagement ends. Build what matters, make it last, leave a codebase (and a
decision trail) the client can read.

The reader to design for is the developer who opens the document years later
because a trade-off has stopped working. Every accepted trade-off therefore
records the conditions it was accepted under — the scale, the quota, the
team's situation at the time — so that reader can tell whether those
conditions still hold, and unwind the decision instead of re-deriving it.

This skill is portable. It assumes nothing about the repo: no specific
tooling, no GitHub, no package manager. Adapt file locations and numbering to
whatever the client's repo already uses; the structure and voice below stay
the same.

## When to write a full RFC vs a decision note

- **Full RFC** — a new subsystem, a mechanism change, a security boundary, or
  anything that will be implemented across multiple PRs. ~150–650 lines.
- **Decision note** — a contained choice (which CSS approach, which testing
  tool). ~30–100 lines: decision, key reasons, alternatives considered,
  conclusion. Still decision-first; just shorter.

When in doubt, write the full RFC. A dense full RFC costs less than a
half-explained note someone has to re-derive.

## The two hard rules

1. **Decision first, justification second.** The document leads with the
   strongest claim it can currently make: a draft states the proposed shape;
   once the decision is made, the RFC is updated so the final decision opens
   the document. Reasons support the decision; they never build up to it.
   Write "We chose X", not "we evaluated several options" — and never dress
   an open question up as a decision. Updating the opening when the decision
   lands is part of the RFC's life, not a rewrite.
2. **The RFC describes the shape of the system, not the shipping order.**
   It says what exists, what talks to what, what the contracts are. A phased
   implementation plan belongs in the RFC only when the phasing is itself a
   design property (a backward-compatible migration, a default that flips
   later). PR slicing and merge order live in the PRs that follow.

## Structure of a full RFC

### Title and metadata

```markdown
# RFC 0012: Title — what it is in one clause

**Status**: Draft | Implemented | Superseded by RFC NNNN
**Date**: YYYY-MM-DD
**Author**: name
```

- Number the file zero-padded: `0012-slug.md`. Follow-ups to an existing RFC
  get a letter suffix: `0004B-follow-up.md`.
- A follow-up RFC opens with one line linking its parent: "This extends
  [RFC 0004](./0004-original.md)" or "This is an update to X".
- A superseded RFC says so in Status and points forward.

### Section order that works

1. **Summary** — the whole decision in one dense paragraph. Name the
   mechanism, the trade-off accepted, and what the reader will find. If the
   reader only reads this, they should be able to repeat the decision
   correctly at a party.

2. **Motivation** — a numbered list of concrete pain points. Each item
   states the problem and its cost. No generalities ("we need scalability" is
   not a pain point; "one read fans out to 6 queries for every user view" is).

3. **System shape / Architecture** — a diagram plus a component table.

   ```markdown
   | Component   | Role                              |
   | ----------- | --------------------------------- |
   | `thing-a`   | the only writer of X              |
   ```

   Diagrams are ASCII blocks for data flow, mermaid for sequences and
   state. Every component in the diagram appears in the table with a
   one-line role.

4. **The mechanism** — how it actually works. This is the bulk:
   - The contract types, verbatim, with the reason each field exists.
   - Action tables — `Action | Payload | Response` — when there is an API or
     invoke surface.
   - Before/after code where the change is a change to a contract.
   - A worked example trace through the system for the main path.

   Name the one thing that everything hangs off ("one mechanism, two kinds,
   one response shape"). If the design has no such sentence, it is not done.

5. **Failure modes** — a table, and be honest in it:

   ```markdown
   | Failure                        | Behaviour                      | Accepted? |
   | ------------------------------ | ------------------------------ | --------- |
   | background generation fails    | logged; nothing appears        | yes       |
   ```

   Accepted-for-now is a legitimate column value — but an acceptance names
   its condition ("yes for now; revisit if report reliability matters"), so
   the years-later reader can tell whether the condition still holds.
   Unaccepted failure modes have a mitigation described somewhere in the RFC.

6. **Out of scope / non-goals** — what this deliberately does not do. Strike
   through items that later got delivered, don't delete them.

7. **Rejected alternatives** — a list. Each item names the alternative and
   the reason it was rejected, in one breath: "X — rejected: the mechanism
   Y already provides it."

8. **Open decisions / open questions** — numbered. Each entry states the
   unresolved thing, why it matters, and what would trigger revisiting it.
   Never hide open decisions; a reviewer finding an unstated open decision
   is how trust in the doc dies.

9. **Risks and mitigations** — a table `Risk | Mitigation`, only for risks
   that are real. Skip the section if there are none.

10. **References** — relative links to sibling RFCs and docs (they move
    with the repo), plus the specific code files the RFC touches. Never a
    generic dump of every file in the repo. Wherever the RFC links code —
    here, inline, or in a table — the link is a permalink pinned to a
    commit: the code the decision was made against, still resolving years
    later when the branch has moved on.

## Voice and habits

These are what make the document read as YLD. They matter more than the
section order.

- **Bold leads on decisions, not on every section.** Sections open with a
  plain declarative sentence. Bold leads belong on decision bullets and
  key-point paragraphs ("**No `?view=` query parameter. Ever.**"), where the
  bolded words alone carry the ruling. No "in this section we will discuss"
  meta-fluff anywhere.

- **Dense, declarative sentences.** No hedging ("we might", "potentially"),
  no throat-clearing, no adjectives doing the work of nouns.

- **Coin a name for each mechanism and reuse it.** One short noun per moving
  part — "the moat", "the registry", "the Google half" — introduced once and
  then used everywhere, never a synonym. If a mechanism has no name, the
  reader re-derives it at every mention.

- **Do the arithmetic on the page.** When a claim is quantitative — calls
  per day against a quota, bytes against a header limit, boilerplate LOC
  against logic LOC — show the numbers and the division, not the adjective.
  "~3,900 calls/day against a 1,000,000/day quota" ends a debate that "well
  within limits" starts.

- **Call out the traps.** When a design has sharp edges, list them plainly:
  "Three things that catch people out:" followed by the bullets. 
  **A reader who skips everything else must not walk into the trap.**

- **Mark what is deliberate, and name the enforcement layer.** Use
  "deliberate", "by construction", "architectural, not enforced by runtime
  checks" to distinguish intent from accident. For every invariant, say what
  holds it up: the compiler ("the `satisfies` enforces this"), a runtime
  check, review convention, or nothing ("only the last step has no compiler
  backstop"). An unguarded invariant stated as guarded is the difference
  between a design and a hope.

- **State trust boundaries explicitly.** "Advisory only — it decides what is
  rendered; the write routes decide what happens." "The body must not be
  trusted here, because…". Every boundary where a check stops or a caller is
  trusted gets a sentence.

- **Honest about annoyances.** A section titled "Known annoyances —
  explicitly not tackled now" is a feature, not an admission. List them, say
  the price of accepting them, and **name the future trigger that reopens
  each one** — the trigger is the written justification for that improvement
  when its time comes.

- **Status tells the truth.** If parts are implemented and parts are
  deferred, say "implemented and tested" for one and "intentionally left to
  follow-up — see Open decisions" for the other, with a link. An RFC that
  lies about its own status poisons every decision after it.

- **Assume the reviewer knows the system.** Readers of these documents
  understand the codebase at a conceptual level. Strip explanation of
  basics; spend the words on the decision, the trade-offs, and the
  consequences. What is obvious to the team is not re-explained.

- **Tables over prose for structured data.** Actions, payloads, failure
  modes, risks, file checklists, boilerplate-vs-logic accounting — all
  tables. Prose is for reasoning, tables for facts.

- **Every code block is identified.** Each code block carries a comment or a
  heading naming the file or contract it illustrates. A code block with no
  provenance is decoration.

## The lifecycle around the RFC

- The RFC lands as its own review request first — **before** implementation.
  The review body: a "What" section with a one-paragraph summary, a note on
  what changed per review feedback, and a line that implementation follows
  once the RFC is reviewed.

- Implementation arrives in **small slices in dependency order**. Each slice
  is one reviewable change; the consolidated description carries a table of
  all slices in merge order with one column saying what each lands and one
  saying why it sits at that position.

- Update the RFC's Status and "Out of scope" as slices land. The RFC is a
  living record until it is superseded, not a snapshot of intent.

## Quick check before calling it done

- [ ] The Summary states the decision; a skimmer could repeat it correctly.
- [ ] Every claim about behaviour is backed by a mechanism section or a
      worked trace.
- [ ] Trust boundaries and "advisory vs enforced" are explicit.
- [ ] Failure modes are tabulated with an honest Accepted column.
- [ ] Open decisions are listed with revisit triggers.
- [ ] Every code block names its file or contract.
- [ ] Status field matches reality.
- [ ] Every file path, symbol and number named in the doc exists and is
      correct at time of writing.
- [ ] Every code link is a permalink pinned to a commit; sibling RFCs and
      docs are relative links.
- [ ] Every accepted trade-off names the condition it was accepted under.
- [ ] No tooling is assumed that the repo does not actually have.

---
name: jira-ticket
description: Writes a Jira ticket (bug, story, task or spike) that another engineer can pick up without asking the reporter anything. Use when an engineer asks to write, raise, draft, file or improve a Jira ticket. Also use when an engineer wants a Slack message, meeting note, support complaint or their own notes turned into a ticket.
---

# Write a Jira Ticket

## Role

You write Jira tickets for engineers who were not in the conversation that
started the work.

## Objective

A ticket that the next engineer can pick up and finish without a call to the
reporter. Every statement in it is either from the source or marked as an
assumption or a question.

## You need

- The source: a Slack message, meeting note, support complaint, error log or
  the engineer's own notes.
- Optional: the Jira project, the components and labels the team uses, and an
  epic to link to.

## Steps

1. Read the source and list what it states as fact. Everything else is a
   guess.
2. Decide the type. **Bug**: something that worked, or should work, does not.
   **Story**: a user can do something new. **Task**: engineering work with no
   change a user sees. **Spike**: a question to answer, time-boxed, with a
   decision as the result.
3. Set the scope. One ticket has one outcome that one person can finish. If
   the source asks for two outcomes, write the first ticket and give the
   second as a one-line suggestion for its own ticket.
4. If a fact that changes the work is missing, ask for it before you write.
   Ask at most three questions. If the user cannot answer, or tells you to go
   ahead, write the ticket and put each gap under Open questions.
5. Write the ticket in the Output format below.
6. If the ticket compares three or more items along the same dimensions, and
   the table-me skill is available, use it to put that part in a table.
   Examples are the options in a spike, or the browsers and environments
   where a bug happens.
7. If the source names a system, protocol or error that an engineer outside
   the team may not know, and the eli5 skill is available, use it with the
   audience "Engineer". Put the result under Background. Background explains
   the term only. It adds no fact about this case.
8. If the asd-ste100 skill is available, apply it last to the ticket text, in
   STE-flavored mode. Keep the section headings, the "Assumption:" labels and
   the hedges from the source.

## Sourcing rule

Each line in the ticket is one of these three:

- **From the source.** Write it plainly.
- **Your inference.** Write it as a statement that starts with
  "Assumption:", and keep it under Open questions, not under Acceptance
  criteria.
- **Unknown.** Put it under Open questions as a question.

Quote the source word for word, or do not quote it.

Keep the source's own hedges. "I think it's the index" stays a hypothesis. Do
not turn it into the cause.

Keep the source's own users. If the source says "users export their invoices",
the story is about those users. Do not swap in a different role.

## Output format

Plain Markdown that pastes into Jira. Use these sections in this order, and
leave out a section only where this list says so.

1. **Title.** At most 80 characters. State the outcome or the symptom, not
   the activity. "Checkout fails when a promo code is applied" is good.
   "Investigate checkout" is not. A cause goes in the title only when the
   source states it as fact. A suspected cause stays out of the title, also
   with "possibly" or "maybe".
2. **Type.** Bug, Story, Task or Spike.
3. **Priority.** Write "For triage" unless the source gives evidence: how many
   users, money lost, a deadline, or a workaround that does not exist. Then
   give the value and that evidence in one line.
4. **Context.** Two to four sentences: what happens now, who it affects, and
   why it matters now. Facts from the source only.
5. **Background.** One or two sentences from step 7. Leave out the section if
   step 7 did not run.
6. **For a Bug:** Steps to reproduce, Expected, Actual. If steps are unknown,
   write "Not reproduced yet" and give what the source says about when it
   happens.
   **For a Story:** one line, "As a [user from the source], I want [action],
   so that [outcome]."
   **For a Spike:** the question to answer and the time box.
   **For a Task:** leave this section out.
7. **Acceptance criteria.** Three to six checks. Each one passes or fails when
   a reviewer looks at it. Use numbers, named screens, named endpoints or named
   tests. "The job finishes in under 30 minutes on the nightly run" is good.
   "Performance is improved" is not. Investigation steps do not go here.
8. **Out of scope.** Things the source mentions that this ticket does not do.
   Leave out the section if there are none.
9. **Open questions.** Each gap and each "Assumption:" line, with who can
   answer it if the source names that person. Leave out the section if there
   are none.
10. **Links.** Links from the source: Slack thread, support tickets, PRs,
   dashboards. Write "[add link]" where the source mentions a thing but gives
   no link.

Keep the ticket under 300 words. If it is longer, the scope is probably two
tickets.

## Creating the ticket in Jira

The default output is the text only. If a Jira connector is available and the
user asks you to create the ticket, show the final text and the target
project, and wait for a yes. Creating the ticket sends its content to Jira.

## Before you hand it over

- Every acceptance criterion is a check, not a task or a wish.
- No number, tool, threshold, column or version appears that the source does
  not state, except on an "Assumption:" line.
- Priority is "For triage", or it has evidence.
- A second outcome in the source is a suggested ticket, not part of this one.

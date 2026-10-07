# jira-ticket

Writes a Jira ticket that another engineer can pick up and finish without
asking the reporter anything. You give it the source: a Slack message, a
meeting note, a support complaint, an error log or your own notes. It gives
back the ticket as Markdown that pastes into Jira.

The agent reads `SKILL.md`. This file is for people.

## Use it

Call it by name, or describe the task and let the agent pick it:

```text
/jira-ticket raise a ticket for this: "checkout is broken again for some people..."
```

From the team plugin, the command is `/yld-engineering:jira-ticket`.

## What the ticket gets right

| Rule | What you see in the ticket |
|---|---|
| Facts come from the source | Anything the agent guessed is on an "Assumption:" line under Open questions |
| Hedges stay hedges | "I think it's the index" stays a hypothesis, and a suspected cause stays out of the title |
| Priority needs evidence | "For triage", unless the source gives a user count, money lost, a deadline or no workaround |
| Acceptance criteria are checks | Each one passes or fails, with numbers or named screens, endpoints and tests |
| One ticket, one outcome | A second outcome becomes a suggested ticket under Out of scope |
| Short | Under 300 words |

If a fact that changes the work is missing, the agent asks at most three
questions before it writes. If you cannot answer, it writes the ticket and
puts the gaps under Open questions.

## Ticket types

| Type | Use it when | Extra section |
|---|---|---|
| Bug | Something that worked, or should work, does not | Steps to reproduce, Expected, Actual |
| Story | A user can do something new | "As a … I want … so that …" |
| Task | Engineering work with no change a user sees | none |
| Spike | A question to answer, time-boxed | The question and the time box |

## Other skills it uses

If these skills are installed, the agent uses them. If they are not, it
skips that step.

| Skill | When | What it adds |
|---|---|---|
| `table-me` | The ticket compares three or more items along the same dimensions | A table |
| `eli5` | The source names a system, protocol or error that engineers outside the team may not know | A Background section, written for engineers |
| `asd-ste100` | Always, as the last step | Plain, short sentences that are hard to misread |

## Creating the ticket in Jira

By default the agent gives you the text only. If a Jira connector is available
and you ask it to create the ticket, it shows the final text and the target
project first, and waits for your yes. Creating the ticket sends its content
to Jira.

## Examples

Each example below is output from a test run of the skill on a short source
note. We removed three lines from the Bug and Task examples. Two stated facts
that were not in the source, and one was an acceptance criterion that nobody
could check.

<details>
<summary><strong>Bug:</strong> a vague Slack message about checkout</summary>

**Source**

> hey, checkout is broken again for some people, saw a few complaints in
> support. think it's the promo code thing from last sprint?? can u raise a
> ticket pls, kinda urgent

**Ticket**

**Title:** Checkout fails for some users

**Type:** Bug

**Priority:** For triage. The reporter calls it "kinda urgent". The source
gives no user count, revenue loss or deadline.

**Context**

Checkout is broken for some users. The reporter saw "a few complaints in
support". The reporter says it has happened before ("again").

**Steps to reproduce**

Not reproduced yet. The source says only that it affects "some people". It
does not say which people, which step fails, or what error they see.

**Expected**

A user can complete checkout.

**Actual**

Checkout fails for some users. The failure is not described further.

**Acceptance criteria**

1. The failing checkout steps are written down in this ticket and reproduced
   in at least one environment.
2. The cause is named in this ticket and confirmed with evidence, such as a
   log line, a failing test or a reproduction.
3. After the fix, the reproduced case completes checkout.
4. A test covers the reproduced case and fails without the fix.
5. The support complaints are reviewed and each one is shown to match the
   fix, or listed as a separate issue.

**Open questions**

- Assumption: the reporter thinks the promo code change from last sprint
  caused this ("think it's the promo code thing"). This is a guess and not a
  confirmed cause. Check it, but do not limit the investigation to it. Who can
  answer: the reporter.
- Assumption: "again" means an earlier checkout failure. Which earlier ticket
  or incident? Who can answer: the reporter.
- Which users are affected, and what do they have in common? Who can answer:
  support.
- What error or behavior do users see, and at which checkout step?
- How many complaints came in, and over what period?
- Is there a workaround for affected users?

**Links**

- Support complaints: [add link]
- Previous checkout issue: [add link]
- Promo code work from last sprint: [add link]
- Slack message: [add link]

</details>

<details>
<summary><strong>Story:</strong> a product manager's note about invoice export</summary>

**Source**

> Users should be able to export their invoices. CSV probably, maybe PDF too.
> Finance team keeps asking us to do it manually. Would be nice for Q1.

**Ticket**

**Title:** Users can export their invoices as CSV

**Type:** Story

**Priority:** For triage

**Context:** Users cannot export their invoices today. The Finance team keeps
asking us to export invoices for them by hand. The PM would like this done in
Q1 ("would be nice", so not a firm deadline).

**Story:** As a user, I want to export my invoices, so that Finance does not
have to ask us to do it manually.

**Acceptance criteria:**

1. A user can start an export of their invoices from the product, without
   help from engineering.
2. The export downloads as a CSV file.
3. The CSV file opens in a spreadsheet application and has one row per
   invoice.
4. The Finance team confirms the file is usable in place of the manual
   export.

**Out of scope:** PDF export. The note says "maybe PDF too". Suggested
separate ticket: "Users can export their invoices as PDF".

**Open questions:**

- Which users can export, and which invoices does each user see? Assumption:
  each user exports only the invoices they can already view.
- Which invoice fields go in the CSV? Assumption: the same fields Finance gets
  in the manual export today.
- Which screen holds the export action? (PM, offline)
- Does the PM want CSV first? The note says "CSV probably". Assumption: yes.
- Is Q1 a firm deadline? (PM)
- Do any invoice sets exist that are too large to export in one request? Not
  stated.

**Links:** [add link] for the Finance requests.

</details>

<details>
<summary><strong>Task:</strong> an engineer's note about a Node upgrade</summary>

**Source**

> The reporting service still runs on Node 16, which is end of life. We should
> move it to Node 22 LTS. The Dockerfile and the CI image both pin node:16. No
> user-facing change expected.

**Ticket**

**Title:** Move the reporting service from Node 16 to Node 22 LTS

**Type:** Task

**Priority:** For triage

**Context**

The reporting service runs on Node 16. Node 16 is end of life. The Dockerfile
and the CI image both pin node:16. No user-facing change is expected.

**Acceptance criteria**

1. The Dockerfile of the reporting service uses a node:22 base image.
2. The CI image for the reporting service uses node:22.
3. The CI pipeline for the reporting service passes on node:22.
4. No file in the reporting service repository pins node:16.

**Open questions**

- Assumption: The Dockerfile and the CI image are the only places that pin
  Node 16. Other places, such as .nvmrc, the "engines" field in package.json,
  or other pipeline files, may also pin it.
- Assumption: Dependencies of the reporting service may need updates to run on
  Node 22. Who can confirm?
- Which exact Node 22 tag does the team want to use?
- How do we confirm that no user-facing change occurs?

**Links**

- [add link] Repository of the reporting service
- [add link] Dockerfile
- [add link] CI image definition

</details>

<details>
<summary><strong>Spike:</strong> a decision between three options, with table-me and eli5</summary>

**Source**

> We keep getting 'idempotency key reuse' 409s from the Stripe webhook handler
> when deliveries retry. Before we change anything we should decide how to
> dedupe: a Redis SETNX lock, a unique constraint on processed_events in
> Postgres, or Stripe's own event ID check in the handler. Compare on effort,
> failure risk and whether it survives a Redis restart. Give it two days max.

**Ticket**

**Title:** Decide how to dedupe retried Stripe webhook events (409s)

**Type:** Spike

**Priority:** For triage

**Context:** The Stripe webhook handler returns "idempotency key reuse" 409s
when deliveries retry. The team wants to decide how to dedupe before it
changes anything.

**Background:** An operation is idempotent when running it twice has the same
effect as running it once. Stripe retries webhook deliveries, so a handler
can receive the same event more than once and must detect repeats.

**Question:** Which dedupe method should the handler use? The options are a
Redis SETNX lock, a unique constraint on `processed_events` in Postgres, or
Stripe's own event ID check in the handler.

**Time box:** 2 days maximum.

| Option | Effort | Failure risk | Survives Redis restart |
|---|---|---|---|
| Redis SETNX lock | ? | ? | ? |
| Postgres unique constraint on `processed_events` | ? | ? | ? |
| Stripe event ID check in handler | ? | ? | ? |

All cells are for the spike to fill in.

**Acceptance criteria**

1. The table above has no "?" cells.
2. The ticket names one chosen option and gives the reason in at most three
   sentences.
3. The ticket states, for each option, what happens to a retried event after
   a Redis restart.
4. A follow-up implementation ticket for the chosen option exists in Jira.

**Out of scope:** Changing the handler. This ticket ends with a decision.

**Open questions**

- Assumption: the 409s come from the duplicate deliveries described in the
  note. Nobody has confirmed this. [add link] to an example log would help.
- Assumption: "failure risk" means the risk of a duplicate or dropped event
  being processed. The author can confirm.
- How often do the 409s happen? The note gives no count.

**Links:** [add link] to the handler code. [add link] to the 409 logs.

</details>

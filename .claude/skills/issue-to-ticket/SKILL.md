---
name: issue-to-ticket
description: Turn a customer or user issue report (a filled-in issue template, support email, chat transcript, or rough notes) into a clear, engineer-ready ticket in Markdown with a precise title, clean reproduction steps, expected vs. actual behavior, environment, evidence, and a definition of fixed. Use this skill whenever someone on a Support team wants to convert, triage, clean up, formalize, or escalate an issue, bug report, or customer complaint into a ticket, bug, or handoff for Engineering, even if they just paste a report and say "make this a ticket" or "get this ready for the devs." Also use it when someone asks for a blank issue or bug report template to send to a reporter.
---

# Issue to Ticket

Turn an issue report written by a reporter (often non-technical) into a ticket an engineer can pick up and start working on without going back to the reporter for basics.

The input is usually a filled-in copy of `assets/issue-template.md` (in this skill's directory), which has: reporter details, what happened, what they expected, steps to reproduce, environment (OS, browser, versions), screenshots or files, and anything else. Freeform input (a forwarded email, a chat log, a few notes) works too; map it onto those same sections.

The person running this skill is typically a Support team member, not the reporter.

**If the user asks for a blank issue template** (for example, to send to a reporter), copy `assets/issue-template.md` into their current working directory as `issue-<slug>.md`, or a name they choose, tell them where it is, and stop there.

## Step 1: Read the report

- Read the whole report, including any attached or linked screenshots, logs, or files you can access. Error text in a screenshot is often the most useful clue in the report.
- **If the work is inside the product's code repository:** search briefly for the error messages, page names, or features the report mentions, so you can point the engineer to likely starting places (Step 3). Keep this to a few minutes; diagnosing the bug is the engineer's job, not this skill's.

## Step 2: Check whether it's ready, then ask once

An engineer needs three things to start:

1. **What went wrong:** the actual behavior, specifically (not just "it's broken")
2. **What should have happened:** the expected behavior
3. **How to get there:** steps that someone else could follow to see the problem

If any of these is missing or too vague to act on, ask the Support person **one batch of no more than 5 short questions**, then wait. Write each question so Support can either answer it themselves or forward it to the reporter as is. Ask only about things that would change what the engineer does first; anything else goes under Open questions in the ticket. If Support says to write it anyway, write it and list the gaps as open questions.

Also check whether this should be an engineering ticket at all. If it's really a **feature request** ("it would be great if..."), say so and suggest the prd-creator skill instead. If it's a **how-to question** with a clear answer, give Support the answer instead of a ticket. If the report describes **several unrelated problems**, write one ticket per problem; mixed tickets get half fixed.

## Step 3: Write the ticket

Principles behind the format:

- **Keep the reporter's evidence intact.** Copy error messages, codes, and quoted text exactly; engineers search code and logs for them, and a "cleaned up" error message matches nothing. Tidy the reporter's prose, but never their evidence.
- **Don't invent facts.** Don't fill in steps, versions, URLs, or error text the reporter didn't give. If a step seems implied, write it and mark it `*(inferred)*`. Unknown environment details are "Not provided," not guesses.
- **Make steps followable by a stranger.** Number them, start from a clear starting point (for example, "Signed in as a standard user, on the Reports page"), and put one action per step. Merge in useful details the reporter mentioned elsewhere in the report.
- **Separate facts from guesses.** Anything you work out yourself, like a likely cause or relevant code, goes only under "Possible starting points" and is worded as a lead, not a conclusion. An engineer who trusts a wrong diagnosis loses more time than one with no diagnosis.
- **Turn relative dates into actual dates.** Reporters write "last month" or "by Friday," but tickets get read weeks later. Work out the actual date from the report date (or today's date, if there isn't one) and write it, keeping the reporter's words when they add meaning: "by Friday (2026-10-02)." If you can't pin down a date, leave the reporter's words as they are.
- **Track missing attachments.** If the report mentions a screenshot, file, or log that isn't attached or that you can't open, list it under Evidence as "referenced but not attached" and add an open question asking the reporter for it. Missing evidence is easy to overlook once the ticket looks complete.
- **Title it like a search result.** Engineers scan ticket lists. A good title names the area and the symptom: "Reports: Export to CSV fails with 'Error 500' for reports over 10k rows," not "Export broken" or "Customer issue."

### Ticket structure

```markdown
# <Area>: <specific symptom>

| | |
|---|---|
| **Type** | Bug |
| **Reported** | <date reported> by <name>, <organization> |
| **Source** | <input file name or "Pasted report"> |

## Summary
<!-- 2-3 sentences: what's wrong, where, and under what conditions. -->

## Steps to reproduce
<!-- Starting point, then numbered steps, one action each. Note how often it happens if known. -->

## Expected behavior

## Actual behavior
<!-- Include error messages verbatim in code formatting. -->

## Environment
- **Operating system:** <name and version, or "Not provided">
- **Browser:** <name and version, or "Not provided">
- <any other relevant details from the report: account type, app version, file type, data size>

## Evidence
<!-- Screenshots, recordings, files, and logs, listed with a one-line note on what each shows. -->

## Additional context
<!-- Recent changes, things the reporter already tried, related tickets. Omit if none. -->

## Possible starting points
<!-- Only if you found something useful: matching error strings or related code (file paths),
     or a plausible hypothesis. Label these as leads. Omit the section if you have nothing. -->

## Definition of fixed
- [ ] Following the steps above produces the expected behavior.
- [ ] <any other specific check, e.g. "The error message no longer appears for files over 10 MB">
- [ ] A regression test covers this case.

## Open questions
<!-- Missing information and who could answer it (reporter or Support). Omit if none. -->
```

Keep it short when the issue is simple. Omit sections that have nothing in them, except Steps to reproduce, Expected behavior, and Actual behavior, which every ticket needs.

## Step 4: Save and report

- Save the ticket as Markdown. If the input came from a file, save it beside that file as `ticket-<slug>.md`; otherwise save it in the current working directory. If the user named a location, use that.
- Then tell the user, briefly:
  - the file path and the ticket title
  - anything marked *(inferred)*, and any open questions, since these are what Support should check before handing it off
  - if you split the report into several tickets, or suggested it isn't a bug, say why

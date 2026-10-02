---
name: prd-creator
description: Turn a short, filled-in PRD input template (or rough feature notes) into a complete, formal Product Requirements Document in Markdown that a developer or AI coding agent can implement from. Use this skill whenever the user wants to write, create, draft, generate, or formalize a PRD, product requirements doc, product spec, or feature spec; when they share notes with user stories, step-by-step flows, or acceptance criteria and want them turned into a spec; or when they ask for a blank PRD template to fill out, even if they don't say "PRD" explicitly.
---

# PRD Creator

Turn a short input template into a full PRD that a human developer or an AI coding agent can build from without needing to come back with questions.

The user fills in the parts only they know: what's being built, why, user stories, step-by-step flows, acceptance criteria, and what's out of scope. Your job is to formalize that and fill in everything an implementer also needs: numbered requirements, testable acceptance criteria, edge cases, security, testing, documentation, and an implementation plan.

## Step 0: Get the input

- **The user wants a blank template:** copy `assets/prd-input-template.md` (in this skill's directory) into their current working directory as `prd-input-<slug>.md`, or a name they choose, and tell them where it is. Stop there; they'll come back when it's filled in.
- **The user points to a filled-in template or pastes notes:** read it. Freeform notes are fine too; map them onto the template's sections in your head.
- **The work is inside a code repository:** take a quick look (README, package manifests, top-level structure) so tech-stack details and naming in the PRD match reality. Keep this brief; the PRD describes *what* to build, not a full code design.

## Step 1: Check for important gaps, then ask once

Before writing, check whether you have enough to write a PRD worth building from. The essentials are:

1. What's being built, and the problem it solves or opportunity it unlocks
2. At least one user story (who wants what, and why)
3. At least one step-by-step flow of how someone uses it
4. Some sense of what "done" looks like

Also look for **big decisions** the input leaves open. A decision is big if it changes:
- **Who can access what:** permissions, sharing, visibility, who can see or change data
- **What data is stored:** new kinds of data, personal or sensitive data, how long it's kept
- **Core behavior:** how the main flow works, or anything that would force a redesign if guessed wrong

Even when the rest of the input is complete, don't assume big decisions; ask about them. A wrong guess here gets built into the foundation and is expensive to undo, while a quick question costs the user a few seconds. (For example, "Does a share link grant access by itself, or only work for people who already have access?" is a big decision, so ask it. "Should the Copy button show a confirmation?" is not.)

If any essential is missing or too vague to act on, something in the input is contradictory, or a big decision is open, ask **one batch of no more than 5 short questions**, then wait. Put the most important questions first. Don't ask about things you can reasonably infer (standard error handling, common security practice, obvious edge cases, small UI details); those become labeled assumptions instead. Asking only once keeps this fast; a long back-and-forth defeats the point of the short template.

If the input is solid with no big decisions left open, or the user says to just write it, skip the questions and go straight to Step 2. If the user says to just write it while big decisions are still open, make your best call and list those decisions under "Please confirm" in Assumptions.

## Step 2: Write the PRD

Use the structure below. Some principles behind it:

- **Implementers take text literally, especially AI agents.** Vague words like "fast", "intuitive", or "secure" don't help an agent. Turn them into checkable statements ("search results appear within 1 second for 10k records").
- **Stable IDs make the PRD easy to reference.** Number user stories (US-1), flows (UF-1), functional requirements (FR-1), and so on, so tasks, commits, and test names can refer back to them. Link requirements to the stories they serve.
- **Keep the user's intent and wording.** Polish their user stories and acceptance criteria into consistent form, but don't change their meaning. Their acceptance criteria are the core of the PRD; expand them, don't replace them.
- **Label what you inferred.** Anything that's a real decision the user didn't make (a limit, a permission rule, a default behavior) gets marked `*(assumed)*` inline and listed under Assumptions, so the user can confirm or correct it quickly. Standard good practice (input validation, accessible markup) doesn't need a label.
- **Never invent business facts.** Don't make up target metrics, dates, budgets, customer names, or compliance requirements. If they matter and are unknown, list them under Open questions.
- **Serve both readers.** Agents need the full detail; people need the gist fast. The **At a glance** block at the top gives people the gist in about 5 lines, so the detail below can stay complete.
- **Scale to the feature.** A small feature gets a short PRD. If a section barely applies, keep it to a line or two; if it doesn't apply, write one line saying so and why (e.g. "Not applicable: no user data is stored"). Don't pad sections to make them look complete.
- **Respect out of scope.** Carry over the user's out-of-scope list without changing its meaning, and don't let requirements quietly creep into it. This matters most for agents, which tend to overbuild.

### PRD structure

```markdown
# PRD: <Feature name>

| | |
|---|---|
| **Status** | Draft |
| **Date** | <today> |
| **Owner** | <from input, or "TBD"> |
| **Source** | <input file name, if any> |

> **At a glance**
> - **What:** <one sentence>
> - **Why:** <one sentence: the problem or opportunity>
> - **Scope:** <N> user stories, <N> requirements (<N> Must); not included: <top 1-2 out-of-scope items>
> - **Build order:** <phases in a few words, e.g. "data model → Share dialog → access requests → disable">
> - **Needs your input:** <number of "Please confirm" assumptions and open questions, or "Nothing">

## 1. Overview
### What we're building
### Problem / opportunity
### Goals
<!-- 2-5 outcomes, drawn from the summary and user stories -->

## 2. User stories
<!-- US-1, US-2, ... in "As a <user>, I want <goal> so that <result>." form -->

## 3. User flows
<!-- UF-1, UF-2, ... numbered steps (User ... / System ...), each with an "Error and alternate paths" sub-list -->

## 4. Functional requirements
<!-- For each requirement: -->
### FR-1: <short name>
- **Priority:** Must | Should | Could
- **Supports:** US-1, UF-1
- **Description:** what the system does, in plain, specific language
- **Acceptance criteria:**
  - [ ] Given <context>, when <action>, then <observable result>
  - [ ] ...

## 5. Non-functional requirements
<!-- Performance, reliability, accessibility, browser/device/platform support, scalability. Only what's relevant; be specific. -->

## 6. Edge cases and error handling
<!-- Empty states, invalid input, limits, concurrency, network or service failures, permission denied. -->

## 7. Security and privacy
<!-- Who can do what, sensitive data handling, input validation, audit or logging needs. -->

## 8. Testing strategy
<!-- Which test types (unit, integration, end-to-end), the critical paths that must have tests, and how each FR's acceptance criteria get verified. -->

## 9. Documentation
<!-- What to write or update: user-facing help, API docs, README, release notes, in-app text. -->

## 10. Out of scope
<!-- From the user's input. Add obvious adjacent items only if they help prevent overbuilding, marked *(assumed)*. -->

## 11. Implementation plan
<!-- Ordered phases. Each phase lists which FRs it covers and ends in something working and testable. Start with the smallest end-to-end slice. -->

## 12. Assumptions
<!-- Every *(assumed)* decision, numbered A-1, A-2, ... and split into two groups. -->
### Please confirm
<!-- At most 3: the assumptions that would change the build if wrong. Say briefly why each one matters. -->
### Minor
<!-- Reasonable defaults (limits, small UI behavior, sensible security practice). One line each. -->

## 13. Open questions
<!-- Unresolved items from the input plus anything unknown that matters. Say what each one blocks, if anything. -->

## 14. Definition of done
<!-- Checklist: all Must FRs meet acceptance criteria, tests pass, docs updated, etc. -->
```

## Step 3: Save and report

- Save the PRD as Markdown. If the input came from a file like `prd-input-<slug>.md`, save it beside that file as `prd-<slug>.md`. Otherwise save `prd-<slug>.md` in the current working directory. If the user named a location, use that.
- Then give the user a short summary in chat, not the whole PRD:
  - the file path
  - how many user stories, flows, and functional requirements it has
  - the "Please confirm" assumptions (one line each), since these are what they most need to review; mention how many minor ones are in the PRD, but don't list them
  - any open questions
- Offer to revise. When the user corrects an assumption, update the PRD and remove the `*(assumed)*` marker.

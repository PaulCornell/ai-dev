# PRD Input: Report share links

## 1. Summary
**What we're building:** A "Share" button on reports that creates a link teammates can open to see the live report.

**Problem it solves / opportunity it unlocks:** Today people export reports to PDF and email them around. The PDFs go stale within days, and people make decisions on old numbers. Live links keep everyone on current data and cut down on export/email busywork.

## 2. User stories
- As a manager, I want to share a report link so that my team always sees current numbers.
- As a teammate, I want to open a shared link without hunting for the report so that I can check numbers quickly.
- As a report owner, I want to turn off a link so that people who shouldn't see the report anymore can't.

## 3. How it works (step by step)
**Flow: Share a report**
1. User opens a report and clicks Share.
2. System shows a dialog with a link and a Copy button.
3. User clicks Copy and pastes the link in Slack.
4. Teammate clicks the link and the report opens.
- If something goes wrong: teammate doesn't have access → show a "Request access" page.

**Flow: Turn off a link**
1. Owner opens the Share dialog.
2. Owner clicks "Disable link".
3. Anyone opening the old link sees "This link is no longer active."

## 4. Acceptance criteria
- [ ] The link always shows the current version of the report.
- [ ] Only people in the same workspace can open the link.
- [ ] Users without access see a "Request access" page, not an error.
- [ ] A disabled link stops working immediately.

## 5. Out of scope
- Public links for people outside the workspace
- Link expiration dates
- Sharing dashboards (reports only for now)

## 6. Notes (optional)
- App is React + Node/Express + Postgres.
- Report access is already controlled by workspace roles (viewer/editor/owner).

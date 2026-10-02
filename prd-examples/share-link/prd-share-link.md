# PRD: Report share links

| | |
|---|---|
| **Status** | Draft |
| **Date** | 2026-10-02 |
| **Owner** | TBD |
| **Source** | prd-input-share-link.md |

> **At a glance**
> - **What:** A Share button that creates a link to the live version of a report, which the owner can disable.
> - **Why:** Emailed PDF exports go stale, so people make decisions on old numbers.
> - **Scope:** 3 user stories, 5 requirements (5 Must); not included: public links, link expiration.
> - **Build order:** link data model → Share dialog → Request access → Disable link → hardening.
> - **Needs your input:** 3 assumptions to confirm, 2 open questions.

## 1. Overview

### What we're building
A **Share** button on each report that generates a link teammates can open to see the live, current version of that report. Report owners can disable a link at any time.

### Problem / opportunity
Today, people share reports by exporting them to PDF and emailing them. Those PDFs go stale within days, so decisions get made on outdated numbers, and the export-and-email routine is busywork. A live link keeps everyone on current data with a single click.

### Goals
- Teammates always see current report data when they follow a shared link.
- Sharing a report takes seconds: click, copy, paste.
- Report owners stay in control of who can reach a report through a link.

## 2. User stories

- **US-1:** As a manager, I want to share a report link so that my team always sees current numbers.
- **US-2:** As a teammate, I want to open a shared link without hunting for the report so that I can check numbers quickly.
- **US-3:** As a report owner, I want to turn off a link so that people who shouldn't see the report anymore can't.

## 3. User flows

### UF-1: Share a report
1. User opens a report and clicks **Share**.
2. System shows a Share dialog containing the report's link and a **Copy** button. If the report has no active link yet, one is created at this point.
3. User clicks **Copy**; system copies the link to the clipboard and shows a brief "Link copied" confirmation.
4. User pastes the link elsewhere (for example, Slack).
5. Teammate clicks the link; system checks that they're signed in, in the same workspace, and have access to the report, then opens the report.

**Error and alternate paths**
- Teammate isn't signed in → system sends them to sign in, then returns them to the report.
- Teammate is signed in but lacks access to the report → system shows a **Request access** page (see FR-4), not an error.
- Teammate is in a different workspace → system shows the **Request access** page without revealing the report's name or contents *(assumed)*.
- Link has been disabled → see UF-2, step 3.
- Clipboard copy fails (browser permissions) → the link stays selected in a text field so the user can copy it manually.

### UF-2: Turn off a link
1. Owner opens the report's Share dialog.
2. Owner clicks **Disable link** and confirms.
3. Anyone who opens the old link sees "This link is no longer active."

**Error and alternate paths**
- Owner wants to share again after disabling → clicking **Share** creates a new link; the old link stays inactive *(assumed)*.

## 4. Functional requirements

### FR-1: Generate a share link
- **Priority:** Must
- **Supports:** US-1, UF-1
- **Description:** Each report can have one active share link at a time *(assumed)*. The link is created the first time someone opens the Share dialog and contains an unguessable token, not just the report ID.
- **Acceptance criteria:**
  - [ ] Given a report with no active link, when a user with editor or owner role *(assumed)* opens the Share dialog, then a link is created and displayed.
  - [ ] Given a report with an active link, when the Share dialog is opened, then the same link is shown (no duplicate links are created).
  - [ ] Given a link token, when it is inspected, then it contains at least 128 bits of randomness and does not expose the report ID.

### FR-2: Copy link
- **Priority:** Must
- **Supports:** US-1, UF-1
- **Description:** The Share dialog includes a **Copy** button that copies the link to the clipboard.
- **Acceptance criteria:**
  - [ ] Given the Share dialog is open, when the user clicks **Copy**, then the link is on the clipboard and a "Link copied" confirmation appears.
  - [ ] Given clipboard access is blocked, when the user clicks **Copy**, then the link text is selected for manual copying.

### FR-3: Open a shared link (live report)
- **Priority:** Must
- **Supports:** US-1, US-2, UF-1
- **Description:** Opening an active link takes an authorized user straight to the report, showing its current data. The link points to the report itself, not a snapshot.
- **Acceptance criteria:**
  - [ ] Given a report updated after the link was created, when an authorized user opens the link, then they see the updated data.
  - [ ] Given a signed-in workspace member with access to the report, when they open the link, then the report opens directly.
  - [ ] Given a user who isn't signed in, when they open the link, then they're asked to sign in and then land on the report.
  - [ ] Given a user from a different workspace, when they open the link, then they cannot see the report.

### FR-4: Request access page
- **Priority:** Must
- **Supports:** US-2, UF-1
- **Description:** A link does **not** grant access by itself; it respects existing report permissions (workspace roles) *(assumed, see A-1)*. Users who reach a report they can't view see a **Request access** page with a button that sends an access request to the report owner.
- **Acceptance criteria:**
  - [ ] Given a workspace member without access to the report, when they open the link, then they see the Request access page, not an error page or 403.
  - [ ] Given the Request access page, when the user clicks **Request access**, then the report owner is notified and the user sees "Request sent."
  - [ ] Given the Request access page, when it is displayed, then it does not reveal report contents.

### FR-5: Disable a link
- **Priority:** Must
- **Supports:** US-3, UF-2
- **Description:** The report owner (and editors *(assumed)*) can disable the active link from the Share dialog. Disabling takes effect immediately.
- **Acceptance criteria:**
  - [ ] Given an active link, when the owner clicks **Disable link** and confirms, then any subsequent request using that link shows "This link is no longer active."
  - [ ] Given a user who already has the report open via the link, when the link is disabled, then their next page load or data refresh shows the inactive message *(assumed)*.
  - [ ] Given a disabled link, when someone opens the Share dialog again, then a new link with a new token is created.
  - [ ] Given a viewer-role user, when they open the Share dialog, then the **Disable link** control is not available.

## 5. Non-functional requirements
- **Performance:** Opening a link adds no more than 200 ms of overhead beyond normal report load time *(assumed)*.
- **Immediacy:** Disabling a link takes effect on the next request; no caching of link validity beyond that.
- **Accessibility:** The Share dialog and Request access page are keyboard-navigable, screen-reader labeled, and meet WCAG 2.2 AA.
- **Compatibility:** Works in all browsers the app currently supports.

## 6. Edge cases and error handling
- Report is deleted → link shows "This report no longer exists."
- Two editors open the Share dialog at the same moment for a report with no link → only one link is created (enforce with a unique constraint on active links per report).
- Malformed or unknown token → same "This link is no longer active" page (don't reveal whether a token ever existed).
- User's access is revoked while they have the report open → next request respects the new permissions.

## 7. Security and privacy
- Tokens are random and unguessable (see FR-1); they don't encode report IDs or user data.
- Every link open re-checks workspace membership and report permissions server-side; the token alone never authorizes access.
- Inactive, unknown, and other-workspace links return responses that don't reveal report names or existence.
- Rate-limit link-resolution requests per IP/user to prevent token guessing *(assumed)*.
- Log link creation and disabling (who, when, which report) for auditing.

## 8. Testing strategy
- **Unit:** token generation (randomness, format), link state transitions (active → disabled → new link).
- **Integration (API):** link resolution against each case: authorized, unauthorized member, other workspace, signed out, disabled, deleted report, malformed token.
- **End-to-end (browser):** UF-1 and UF-2 in full, including the Copy button and Request access flow.
- **Concurrency:** simultaneous Share dialog opens produce exactly one active link.
- Every acceptance criterion in Section 4 maps to at least one automated test.

## 9. Documentation
- Help center article: "Sharing a report with your team" (how to share, who can open links, how to disable).
- In-app text: Share dialog copy, "Link copied", Request access page, inactive-link page.
- API docs for any new endpoints (create/get link, disable link, resolve link, request access).
- Release notes entry.

## 10. Out of scope
- Public links for people outside the workspace
- Link expiration dates
- Sharing dashboards (reports only for now)
- Approving or denying access requests inside this feature; this uses the existing access management *(assumed)*

## 11. Implementation plan
1. **Phase 1: Link data model and resolution** (FR-1, FR-3). Postgres table for share links (report ID, token, status, created by/at, disabled by/at) with a unique constraint on one active link per report; Express endpoints to create/get and resolve links with permission checks. *Result:* a link can be created by API and opened by an authorized user.
2. **Phase 2: Share dialog** (FR-1, FR-2). React Share button and dialog with Copy. *Result:* UF-1 works end to end for authorized users.
3. **Phase 3: Request access** (FR-4). Request access page and owner notification. *Result:* unauthorized members get a useful page instead of an error.
4. **Phase 4: Disable link** (FR-5). Disable control, confirmation, inactive-link page. *Result:* UF-2 works end to end.
5. **Phase 5: Hardening.** Rate limiting, audit logging, accessibility pass, docs.

## 12. Assumptions

### Please confirm
- **A-1:** A link doesn't grant access on its own; it respects existing workspace roles, and users without report access see Request access. *(This decides the whole permission model. The input says both "only people in the same workspace can open the link" and "users without access see Request access"; this reading satisfies both.)*
- **A-3:** Editors and owners can create and disable links; viewers can copy an existing link but not disable it. *(Decides who controls access.)*
- **A-8:** Approving access requests is handled by existing access management, not this feature. *(If wrong, this feature needs a whole approval flow.)*

### Minor
- **A-2:** One active link per report; disabling and re-sharing creates a new link.
- **A-4:** Users from other workspaces see the Request access page without report details.
- **A-5:** An already-open report reflects a disabled link on its next page load or refresh, not instantly mid-view.
- **A-6:** Link resolution adds ≤ 200 ms overhead.
- **A-7:** Link-resolution requests are rate-limited.

## 13. Open questions
- **Q-1:** How should the owner be notified of an access request: email, in-app notification, or both? *(Blocks FR-4 notification details; Phase 3.)*
- **Q-2:** Do we need analytics on link usage (opens per link) to measure the reduction in PDF exports? *(Doesn't block the build.)*

## 14. Definition of done
- [ ] All Must requirements (FR-1 to FR-5) meet their acceptance criteria.
- [ ] Automated tests cover every acceptance criterion; all tests pass.
- [ ] Security checks in Section 7 are implemented and reviewed.
- [ ] Share dialog and Request access page pass an accessibility check.
- [ ] Help article, in-app text, API docs, and release notes are written.
- [ ] "Please confirm" assumptions (A-1, A-3, A-8) confirmed or corrected by the owner.

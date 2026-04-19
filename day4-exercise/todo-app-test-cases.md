# ToDo App Test Cases

This file lists project-specific test cases for the `todo-app` in Module 06, based on the 10 testing headings documented in [README.md](C:/Users/2000105127/codex-training-labs/Codex-modules/modules/module-06-test-driven-development/testing-prompts/README.md).

The scenarios below are aligned with:

- Backend API in [backend/src/index.js](C:/Users/2000105127/codex-training-labs/Codex-modules/modules/module-06-test-driven-development/todo-app/backend/src/index.js)
- Frontend UI in [frontend/src/App.jsx](C:/Users/2000105127/codex-training-labs/Codex-modules/modules/module-06-test-driven-development/todo-app/frontend/src/App.jsx)

---

## 1. Unit Testing

### Test case 1.1: `validateTaskPayload` accepts a valid title
- Objective: Verify valid task payload passes validation.
- Precondition: Function is imported or exposed for unit testing.
- Input: `{ title: "Buy groceries" }`
- Expected result: Function returns `null`.

### Test case 1.2: `validateTaskPayload` rejects missing title
- Objective: Ensure title is required.
- Input: `{}`
- Expected result: Function returns `"Task title is required."`

### Test case 1.3: `validateTaskPayload` rejects whitespace-only title
- Objective: Prevent empty-looking values from being accepted.
- Input: `{ title: "   " }`
- Expected result: Function returns `"Task title is required."`

### Test case 1.4: task creation trims `notes`
- Objective: Confirm notes are normalized before storage.
- Input: `notes: "  call manager tomorrow  "`
- Expected result: Stored task has `notes: "call manager tomorrow"`

---

## 2. Integration Testing

### Test case 2.1: POST then GET returns created task
- Objective: Verify task creation persists in the in-memory list.
- Steps:
1. POST `/tasks` with title, due date, and notes.
2. Assert status `201`.
3. GET `/tasks`.
- Expected result: Response contains the created task at or near the top of the list with `completed: false`.

### Test case 2.2: PATCH toggles completion state
- Objective: Ensure completion endpoint updates an existing task.
- Steps:
1. POST `/tasks`.
2. PATCH `/tasks/:id/complete`.
3. PATCH `/tasks/:id/complete` again.
- Expected result: First PATCH returns `completed: true`, second returns `completed: false`.

### Test case 2.3: invalid POST does not mutate task list
- Objective: Verify failed validation does not add broken tasks.
- Steps:
1. GET `/tasks` count.
2. POST `/tasks` with blank title.
3. GET `/tasks` count again.
- Expected result: Status is `400` and count remains unchanged.

---

## 3. End-to-End Testing

### Test case 3.1: user creates a task from the UI
- Objective: Validate complete flow from form submission to rendered task card.
- Steps:
1. Open frontend.
2. Enter title, due date, and notes.
3. Click `Save task to backend`.
- Expected result: Success status message appears and new task is shown in the task list.

### Test case 3.2: user toggles task completion from the UI
- Objective: Validate full UI-to-backend toggle flow.
- Steps:
1. Create a task.
2. Click `Mark complete`.
- Expected result: Task card updates to completed styling and button text changes to `Mark undone`.

### Test case 3.3: user sees backend validation error in UI
- Objective: Ensure backend errors surface clearly in the frontend.
- Steps:
1. Submit the form with invalid or blank title data.
2. Observe status area.
- Expected result: Error message is displayed and task is not added to the list.

---

## 4. Front-End Testing

### Test case 4.1: required title field blocks empty submit
- Objective: Verify browser-level required behavior exists on the title input.
- Expected result: Form cannot be submitted without a title.

### Test case 4.2: successful submit clears form fields
- Objective: Confirm local form state resets after successful save.
- Steps:
1. Fill title, due date, notes.
2. Mock successful POST response.
3. Submit form.
- Expected result: All input fields reset to empty values.

### Test case 4.3: status message shows success on saved task
- Objective: Verify feedback is shown after successful create action.
- Expected result: Status area contains `Task saved to the backend.`

### Test case 4.4: initial load shows empty-state message
- Objective: Check UI for zero-task scenario.
- Precondition: Mock GET `/tasks` to return `[]`.
- Expected result: `No tasks yet. Submit one above.` is rendered.

---

## 5. API Testing

### Test case 5.1: `GET /tasks` returns JSON array
- Objective: Validate response contract.
- Expected result:
- Status `200`
- `Content-Type` is JSON
- Body is an array

### Test case 5.2: `POST /tasks` requires `title`
- Objective: Confirm API contract rejects invalid payload.
- Input: `{ notes: "missing title" }`
- Expected result:
- Status `400`
- Body contains `{ error: "Task title is required." }`

### Test case 5.3: `POST /tasks` returns expected task shape
- Objective: Validate response schema for created tasks.
- Expected result: Response includes `id`, `title`, `notes`, `due`, `completed`, and `createdAt`.

### Test case 5.4: `PATCH /tasks/:id/complete` returns `404` for bad ID
- Objective: Verify missing resources are handled correctly.
- Expected result:
- Status `404`
- Body contains `{ error: "Task not found." }`

---

## 6. Performance Testing

### Test case 6.1: `GET /tasks` under repeated load
- Objective: Check whether the task list endpoint stays responsive.
- Scenario: Run 200 consecutive GET requests.
- Expected result: Median response time stays within acceptable local threshold such as `150ms`.

### Test case 6.2: concurrent task creation load
- Objective: Measure behavior when many users add tasks simultaneously.
- Scenario: Ramp to 50 virtual users POSTing unique titles.
- Expected result:
- API remains available
- No `5xx` responses
- 95th percentile response time stays within agreed limit

### Test case 6.3: toggle endpoint remains stable under burst traffic
- Objective: Ensure toggle endpoint does not degrade sharply.
- Scenario: Burst PATCH requests against pre-created tasks.
- Expected result: Responses remain successful and no task corruption is observed.

---

## 7. Security Testing

### Test case 7.1: script injection attempt in title
- Objective: Check handling of unsafe-looking input.
- Input: `<script>alert('xss')</script>`
- Expected result: API should either reject unsafe content or safely store/render it without execution.

### Test case 7.2: oversized title payload
- Objective: Evaluate resilience against very long input.
- Input: title with thousands of characters.
- Expected result: App should reject with `400` or handle safely without crashing.

### Test case 7.3: malformed JSON payload
- Objective: Ensure server handles invalid request bodies safely.
- Scenario: Send broken JSON to POST `/tasks`.
- Expected result: Server returns client/server error gracefully and does not crash.

### Test case 7.4: CORS behavior review
- Objective: Validate current CORS setup is intentional.
- Expected result: Cross-origin requests behave according to configured policy in the backend.

---

## 8. Regression Testing

### Test case 8.1: create-task flow still works after UI updates
- Objective: Prevent breakage in main add-task journey.
- Expected result: Task can still be created and displayed after changes.

### Test case 8.2: toggle flow still works after backend refactor
- Objective: Ensure task completion feature remains intact.
- Expected result: Existing tasks still switch between completed and incomplete states.

### Test case 8.3: task ordering remains newest-first
- Objective: Protect current behavior from accidental change.
- Steps:
1. Create task A.
2. Create task B.
- Expected result: Task B appears above task A because backend uses `unshift`.

---

## 9. Smoke Testing

### Test case 9.1: backend starts successfully
- Objective: Quick validation before deeper tests.
- Steps:
1. Start backend.
2. Call `GET /tasks`.
- Expected result: Server responds with `200` and an array.

### Test case 9.2: frontend loads main page
- Objective: Confirm app is reachable.
- Steps:
1. Start frontend.
2. Open `/`.
- Expected result: Main heading `ToDo App (React + Node)` appears.

### Test case 9.3: one create request succeeds
- Objective: Basic proof that core API flow works.
- Steps:
1. POST `/tasks` once.
- Expected result: Status `201` and returned task includes generated `id`.

---

## 10. Acceptance Testing

### Test case 10.1: user can record a personal reminder
- Objective: Validate main business journey.
- Scenario:
1. User enters a reminder title.
2. User optionally adds due date and notes.
3. User saves task.
- Expected result: Reminder appears in the app and is stored by the backend.

### Test case 10.2: user can track completion status
- Objective: Validate the app supports basic task progress tracking.
- Scenario:
1. User creates a task.
2. User marks it complete.
3. User later marks it undone.
- Expected result: Task status reflects both actions correctly.

### Test case 10.3: user gets clear feedback on save outcome
- Objective: Ensure user experience is understandable.
- Scenario:
1. Successful submission.
2. Failed submission due to invalid data or backend issue.
- Expected result:
- Success message appears on valid save
- Error message appears when save fails

---

## Suggested File Usage

You can use these cases to create:

- Jest unit tests
- Supertest integration tests
- React Testing Library UI tests
- Cypress or Playwright E2E suites
- Manual QA checklists

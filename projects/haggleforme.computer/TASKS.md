# TASKS.md

Implement the minimal MVP described in `SPEC.md`, following the defaults and decision rules in `AGENTS.md`.

The goal is a working, polished, minimal implementation of the product defined in `SPEC.md`, not a full platform build.

## Task 1: Scaffold The App
- Create a Vite + React + TypeScript app.
- Add plain CSS stylesheets and set up a clean baseline stylesheet.
- Keep the app single-page.
- Add a simple FastAPI backend with entrypoint `backend/main.py`.
- Create a clear source structure with separate areas for UI components, negotiation logic, API helpers, and tests.

### Subtasks
- Set up the main app shell and render path.
- Add any minimal dependencies needed for the spec.
- Avoid adding libraries that are not clearly necessary.

## Task 2: Define The Core Data Model
- Create TypeScript types for roles, prompts, leaderboard entries, transcript rounds, match results, and negotiation responses.
- Keep the types explicit and reusable across frontend and backend boundaries where practical.

### Subtasks
- Add a `Role` type with `buyer` and `seller`.
- Add typed shapes for:
  - prompt templates
  - tournament match results
  - aggregate results
  - leaderboard state
- Add small utility functions for currency formatting and score calculation.

## Task 3: Add Seed Data
- Create the fixed seeded strategies required by `SPEC.md`.
- Include seeded buyer and seller strategies for `Emaad` and `Alex`.

### Subtasks
- Store the prompt templates in a maintainable location.
- Store seeded strategies in a separate constants/module file.
- Make it easy for the negotiation engine to select the correct opponent pool based on the user's role.

## Task 4: Build The Backend Negotiation API
- Implement a minimal FastAPI server in `backend/main.py` so the OpenAI API key is never exposed in browser code.
- Add:
  - `POST /api/negotiate`

### Subtasks
- Read `OPENAI_API_KEY` from environment variables.
- Read optional `NEGOTIATION_MODEL` from environment variables.
- Return a helpful server error if `OPENAI_API_KEY` is missing.
- Keep the backend in one file unless the spec clearly requires more structure.
- Keep the API response shape simple and aligned with the UI's needs.

## Task 5: Implement Prompt Construction
- Build buyer and seller system prompts exactly as specified in `SPEC.md`.
- Insert the user strategy into the correct prompt template.
- Insert seeded opponent strategies into the opposite-side template.

### Subtasks
- Keep prompt construction in small pure functions.
- Prefer one source of truth for prompt-template text when that is easy to maintain.
- Make the active prompt available to the frontend `View system prompt` modal without adding a dedicated prompt endpoint.
- It is acceptable to duplicate prompt-template text intentionally between frontend and backend if that keeps the implementation simpler.

## Task 6: Implement The Negotiation Loop
- Build the multi-turn negotiation engine on the server.
- Run one tournament match per seeded opponent on the opposite side.

### Subtasks
- Start each match with the exact required start message:
  - `Let's begin the negotiation. Please make your initial offer and inquiries.`
- Ensure the buyer always speaks first.
- Alternate buyer and seller messages.
- Stop when one side accepts or after 12 total rounds.
- Parse acceptance text robustly for prices like `$20,000`.
- Preserve a transcript for every round.

## Task 7: Compute Match Outcomes
- Compute agreement status, agreed price, user surplus, opponent surplus, and total surplus.

### Subtasks
- Use the exact scoring rules from `SPEC.md`.
- Return `0` surplus for both sides when no agreement is reached.
- Aggregate total surplus across the full tournament.

## Task 8: Build The Main UI Shell
- Build the single-page layout using the minimal visual direction defined in `SPEC.md`.

### Subtasks
- Use a warm off-white page background.
- Center a faux terminal window/card with:
  - a thin border
  - strong offset shadow
  - desktop two-column layout
  - stacked mobile layout
- Show the exact title:
  - `haggle for me, computer`

## Task 9: Build The Strategy Submission Panel
- Implement the left column submission experience.

### Subtasks
- Add the `Negotiation Strategy` section heading.
- Add `Buyer` and `Seller` toggle buttons.
- Default to `Buyer`.
- Add the strategy textarea with role-specific placeholder text from `SPEC.md`.
- Add `View system prompt` and `Submit` buttons.
- Prevent empty submissions.
- Disable submit while a negotiation run is in progress.

## Task 10: Build The Prompt Modal
- Add a modal or overlay that shows the currently active system prompt template.

### Subtasks
- Open from the `View system prompt` button.
- Show the correct template for the currently selected role.
- Make the modal keyboard accessible.
- Provide a clear close action.

## Task 11: Build The Results View
- After a successful submission, show a results modal or results panel.

### Subtasks
- Show:
  - your role
  - opponent role
  - total surplus
  - one result block per opponent
- For each opponent block, show:
  - opponent name
  - agreed price or `N/A`
  - your surplus
  - opponent surplus
  - transcript

## Task 12: Render Transcripts Cleanly
- Display transcripts as chat-like round logs.

### Subtasks
- Label messages by role as `buyer` or `seller`.
- Preserve message order exactly.
- Make long messages wrap cleanly.
- Keep the transcript legible on both desktop and mobile.

## Task 13: Build The Leaderboard
- Add a simple local leaderboard in the right column.

### Subtasks
- Show the heading `Leaderboard Top 10`.
- Seed the leaderboard with `Emaad` and `Alex`, each starting at `0` total surplus.
- Add or update a local `You` entry after each submission.
- Store leaderboard data in `localStorage`.
- Sort by total surplus descending.
- Limit display to the top 10 rows.

## Task 14: Add The Explanatory Right-Rail Copy
- Add a short explanatory block under the leaderboard.

### Subtasks
- Explain:
  - this is an agentic used-car negotiation simulation
  - seller fallback is `$18,000`
  - buyer fallback is `$22,000`
  - both sides delegate to AI agents
  - surplus is measured against the fallback option
- Keep the copy concise and readable.

## Task 15: Handle Loading And Errors
- Make the app feel responsive and understandable during negotiation runs.

### Subtasks
- Show a visible loading state while tournaments are running.
- Surface backend failures with user-friendly error messages.
- Avoid leaving the UI in a stuck state after an error.

## Task 16: Add Minimal Persistence
- Persist only what is useful for the MVP.

### Subtasks
- Persist leaderboard state in `localStorage`.
- Do not add accounts, authentication, or shared persistence.
- Do not add a database.

## Task 17: Add Environment Documentation
- Include minimal setup information for running the app locally.

### Subtasks
- Add `.env.example` if needed.
- Document required environment variables.
- Keep the setup short and directly useful.

## Task 18: Test Important Behavior
- Add focused automated tests for the highest-value logic.

### Subtasks
- Test score calculation.
- Test acceptance-price parsing.
- Test role-based opponent selection.
- Test at least one happy path for result rendering or the main submission flow.

## Task 19: Final Polish And Verification
- Make sure the result feels intentional rather than scaffold-like.

### Subtasks
- Check desktop and mobile layout.
- Check button states and modal behavior.
- Check prompt viewing behavior.
- Check leaderboard persistence.
- Run tests and fix obvious breakages.
- Remove dead code, placeholder text, and half-wired features.

## Non-Goals
- Do not add authentication.
- Do not add share links.
- Do not add public multi-user matchmaking.
- Do not add async queues or background workers unless absolutely required by the chosen dev setup.
- Do not over-engineer the MVP.

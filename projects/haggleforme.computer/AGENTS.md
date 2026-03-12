# AGENTS.md

This repository uses `AGENTS.md` as the implementation guide.

This file provides the engineering defaults.
`SPEC.md` provides the product requirements.
Codex should read both, then implement the project.

## Mission
Build a polished, working Vite app that implements the product described in `SPEC.md`.

`SPEC.md` owns the product idea and feature requirements.
This file owns the engineering defaults, implementation standards, and decision rules.

When `SPEC.md` is vague, make strong reasonable decisions and keep moving.
Do not hand the design burden back to the spec author unless a missing detail would create major product risk.

## Source Of Truth
Use this precedence order:

1. Explicit user directions in chat
2. `SPEC.md`
3. This `AGENTS.md`
4. Sensible implementation judgment

Interpretation rule:
- `SPEC.md` controls what the app should do.
- `AGENTS.md` controls how the app should be built, unless `SPEC.md` explicitly requires a different approach.

If there is a conflict, honor the higher-precedence source and note the assumption briefly in the final summary.

## Required Workflow
Follow this sequence:

1. Read `SPEC.md` fully before making changes.
2. Infer the smallest complete product that satisfies the spec.
3. Implement the app end-to-end, not just scaffolding.
4. Run the project checks.
5. Fix obvious issues before finishing.

Default mindset:
- Prefer shipping a complete, coherent version over leaving TODOs.
- Prefer simple architectures over clever ones.
- Prefer high-quality defaults over broad configurability.

## Standard Tech Stack
Unless `SPEC.md` explicitly requires otherwise, use this stack:

- Build tool: Vite
- Language: TypeScript
- UI framework: React
- Styling: plain CSS stylesheets by default
- Backend: FastAPI in Python
- Backend entrypoint: `backend/main.py`
- Package manager: use whatever lockfile already exists; if none exists, use `npm`

Do not introduce extra libraries unless they clearly reduce complexity or are required by `SPEC.md`.

Avoid swapping foundational tools on preference alone.
For example, do not replace React with another framework, switch away from simple stylesheet-based styling without a clear reason, or replace FastAPI with a different backend stack unless the spec clearly calls for it.

## App Architecture Defaults
Use a simple structure that fits the project size.

Start small and add folders only when they clearly improve clarity.
Do not create a deep or highly segmented folder tree for a small app.

Backend default:

- Use a simple FastAPI backend in [backend/main.py](/Users/emaad/Documents/Starter/backend/main.py).
- Prefer a single-file backend for small and medium projects.
- Split the backend into multiple files only when the spec clearly justifies the added structure.
- Keep backend APIs narrow and directly aligned with the UI needs.

Architecture rules:

- Keep domain logic out of presentational components.
- Separate agent behavior, app state, and UI rendering.
- Build small reusable components when repetition appears.
- Keep files focused; split large files before they become hard to navigate.
- Favor composition over inheritance or deep abstraction.

## Conversational Agent Defaults
These projects are conversational-agent apps, so implement around a shared baseline unless the spec says otherwise.

Default UX expectations:

- A conversation-oriented interface is the primary surface.
- This may be a direct chat UI, a strategy/prompt control surface, or another interaction centered on agent dialogue.
- Conversation history, transcripts, or agent outputs should be visible and clearly styled by role when they are part of the product.
- There is an obvious loading/responding state.
- Input is keyboard-friendly and works well on laptop-sized screens.
- The layout is responsive and usable on mobile.

Default product behavior:

- Represent messages with typed objects.
- Preserve enough structure to support future tool use, metadata, or citations.
- If the spec involves personas, goals, or system prompts, model them explicitly rather than burying them in JSX.
- Prefer React state first.
- Introduce a shared state library only if the app complexity clearly justifies it.

If real model access is required:

- Never hardcode secrets.
- Put secrets in environment variables.
- Do not expose provider API keys in browser-only client code.
- If the spec needs secure provider calls, create a minimal server layer or local proxy only if necessary.

If the spec does not require live model access, it is acceptable to simulate or stub the agent behavior, but make the simulation intentional and well-structured.

## UI And Design Standards
Aim for a clean, modern interface.

Required standards:

- Use plain CSS stylesheets consistently unless the spec explicitly requires a different styling system.
- Define a clear visual hierarchy.
- Keep spacing, sizing, and typography consistent.
- Support both desktop and mobile layouts.
- Use semantic HTML and accessible labels.
- Provide visible focus states.
- Ensure sufficient color contrast.

Avoid:

- Unstyled default HTML
- Inconsistent button and input patterns
- Giant walls of text
- Overdesigned animation-heavy interfaces that distract from the agent experience

## Data And State Rules
Be intentional about where state lives.

- Use local component state for small UI-only concerns.
- Use React state by default for app and conversation state.
- Introduce a dedicated shared state library only when prop drilling or cross-feature coordination becomes genuinely cumbersome.
- Derive computed state when possible instead of duplicating it.
- Validate external or user-shaped data with lightweight TypeScript-first patterns unless the spec clearly calls for a schema library.
- Prefer explicit TypeScript types over loose `any` usage.

Do not introduce a database, auth system, or backend persistence unless the spec calls for it.

If persistence is useful and the spec does not forbid it, prefer lightweight browser persistence such as `localStorage`.

## Routing Rules
Default to a single-page experience unless the spec clearly benefits from multiple routes.

When multiple routes are needed:

- Use React Router.
- Keep route structure simple and obvious.
- Avoid deep nesting unless it materially improves the product.

## API Integration Rules
When the app calls APIs:

- Centralize API access in one frontend module or feature area.
- Handle loading, error, and empty states explicitly.
- Show user-friendly error messages.
- Avoid scattering fetch logic across many components.

If the spec requires streaming responses, implement them cleanly and keep partial-response rendering isolated from the rest of the UI.

## Testing Expectations
Every project should include meaningful automated tests.

Minimum expectation:

- Add tests for the most important user-facing logic.
- Test at least one happy path for the main conversational flow.
- Test any nontrivial parsing, validation, or state transitions.

If you add frontend tests in a Vite app, prefer Vitest.

Do not spend project time chasing exhaustive coverage if the app is small.
Favor a few high-value tests over many shallow ones.

## Definition Of Done
The project is done when all of the following are true:

- The app matches `SPEC.md` in a clear and usable way.
- The codebase follows the default stack and structure unless the spec justified a deviation.
- The app runs locally.
- Core interactions work end-to-end.
- Obvious edge cases are handled.
- Types are clean enough to avoid fragile code.
- Tests exist for important behavior.
- The final result feels intentional, not scaffold-like.

## Decision Heuristics
When the spec leaves room for interpretation, use these defaults:

- Choose the simpler implementation.
- Choose the version that improves clarity for the user.
- Choose maintainability over novelty.
- Choose explicitness over hidden magic.
- Choose one strong default instead of many settings.

Do not generate large speculative architectures for small apps.
Do not gold-plate.
Do not leave placeholder text, dead code, or half-wired features.

## Implementation Notes For Codex
When implementing from `SPEC.md`:

- Summarize the inferred product briefly in your own head before coding.
- Make reasonable assumptions without blocking unless the spec is genuinely contradictory.
- If a requirement is ambiguous, resolve it in the most practical way and continue.
- Keep final explanations short and concrete.
- Mention any major assumptions or spec gaps you had to resolve.

## If `SPEC.md` Is Weak
If the spec is incomplete but still usable:

- Implement the strongest coherent version implied by the spec.
- Fill in missing details with conventional conversational-agent patterns.
- Preserve the spec’s core idea.

If the spec is severely contradictory or missing the core product definition:

- Implement the smallest plausible interpretation.
- Call out the ambiguity briefly in the final message.

## Non-Goals
Unless explicitly requested in `SPEC.md`, do not add:

- Authentication
- Database infrastructure
- Complex backend services
- Admin panels
- Internationalization
- Analytics
- Premature plugin systems
- Excessive configuration surfaces

## Deliverable Shape
The finished repository should usually include:

- A working Vite app
- Clear source structure
- A small set of focused components
- Any required environment variable documentation if applicable
- Tests for important behavior

If you create additional files such as `.env.example`, keep them minimal and directly useful.

# SPEC.md

## Product Summary
Build a single-page web app titled **Haggle For Me, Computer**.

The app lets a user write a negotiation strategy for an AI buyer or seller agent negotiating over a used **2020 Toyota Camry LE**. After submission, the app runs a small tournament where the user's agent negotiates against a fixed pool of opponent agents on the opposite side. The app then shows the negotiation transcripts and the resulting surplus scores.

This app should have these defining qualities:

- a strategy-driven AI negotiation game about buying or selling a used car
- fixed negotiation rules and fixed seeded opponents
- buyer and seller system prompts built from user-written strategies
- a terminal-like interface with a restrained retro-computing feel
- a deliberately small feature set focused on the core interaction

## Scope
Implement the following:

- a Vite + React + TypeScript frontend
- plain CSS stylesheets for styling
- a polished single-page interface
- buyer/seller role toggle
- strategy textarea
- modal or panel for viewing the active system prompt
- server-side negotiation execution using a real LLM API
- a simple FastAPI backend in `backend/main.py`
- tournament results with transcripts
- a simple leaderboard

Do **not** implement these for the MVP:

- user accounts
- sign in / registration
- share links
- public multi-user persistence
- developer-status footer
- source-code corner ribbon
- background job queue or polling workflow

The entire submission can run synchronously from one submit action.

## Core User Story
The user visits the page, chooses whether they want to be the buyer or seller, writes a negotiation strategy, submits it, and sees how their agent performs against a small set of predefined opponent strategies.

## Page Structure
Create a single page with this layout:

- full-screen warm off-white background
- centered faux terminal window/card
- thin gray border
- strong offset shadow to give a slightly retro, printed-terminal feel
- desktop: two columns
- mobile: stacked vertically

### Header
The header should prominently display this exact title:

`haggle for me, computer`

Use a monospace or terminal-like treatment for the title.

## Left Column: Strategy Submission
The main interaction area should include:

- section title: `Negotiation Strategy`
- two role buttons: `Buyer` and `Seller`
- a large textarea for entering the user's strategy
- a `View system prompt` button
- a `Submit` button

### Role Toggle
The app starts in `Buyer` mode by default.

Switching roles should:

- update the active system prompt
- update the textarea placeholder text
- update the tournament opponent pool

### Textarea Placeholder Copy
Use these placeholder texts.

For buyer:

`Offer a deal a few thousand under the KBB range and go up by a few hundred dollars as needed with the aim of closing as low as possible.`

For seller:

`Start with an offer that’s a few thousand dollars above the midpoint of the blue book value range. Go down by $500–$1000 each round if necessary. Aim to close a deal as far above the trade-in price as possible.`

### View System Prompt
When the user clicks `View system prompt`, open a modal or overlay showing the currently active system prompt template for the selected role.

The prompt templates may be stored locally in frontend code, backend code, or duplicated intentionally in both places. They do not need their own API endpoint.

## Right Column: Leaderboard And Explanation
The right column should contain:

- heading: `Leaderboard Top 10`
- a compact leaderboard showing player name and total surplus
- a short explanatory text describing what the project is doing

The explanatory text should communicate these ideas:

- this app simulates agentic negotiation for a used car
- the seller's outside option is `$18,000`
- the buyer's outside option is `$22,000`
- both sides delegate negotiation to AI agents
- surplus is the difference between the agreed price and the side's fallback option

## Negotiation Scenario
All negotiations are about the same item:

- used car: `2020 Toyota Camry LE`
- market value range: `$18,000` to `$22,000`
- car history: `No accidents reported`

## System Prompt Templates
Use these exact prompt templates, with the user's submitted strategy inserted where indicated.

### Buyer Prompt Template
```text
You are my negotiation agent for purchasing a used 2020 Toyota Camry LE. Your goal is to maximize my surplus (difference between my fallback price and final price).

Negotiation structure:
- This negotiation will last up to 12 rounds (6 offers from each side)
- You must reach agreement within these 12 rounds or the deal fails
- The negotiation starts with the buyer's first message (round 1)
- Your final message should clearly state acceptance or rejection
- If you accept an offer, reply exactly: I accept the offer of $X.
- If you're close to agreement in the final round, consider accepting to avoid failure

Negotiation parameters (common knowledge):
- Market value range is $18,000 to $22,000 depending on condition
- Car history: No accidents reported

Private information (DO NOT DISCLOSE DIRECTLY):
- Fallback option: I can buy a similar Camry from a dealer for $22,000

Negotiation strategy:
{strategy}
```

### Seller Prompt Template
```text
You are my negotiation agent for selling my used 2020 Toyota Camry LE. Your goal is to maximize my surplus (difference between final price and my fallback price).

Negotiation structure:
- This negotiation will last up to 12 rounds (6 offers from each side)
- You must reach agreement within these 12 rounds or the deal fails
- The negotiation starts with the buyer's first message (round 1)
- Your final message should clearly state acceptance or rejection
- If you accept an offer, reply exactly: I accept the offer of $X.
- If you're close to agreement in the final round, consider accepting to avoid failure

Negotiation parameters (common knowledge):
- Market value range is $18,000 to $22,000 depending on condition
- Car history: No accidents reported

Private information (DISCLOSE STRATEGICALLY):
- Fallback option: I can sell to a dealer at $18,000

Negotiation strategy:
{strategy}
```

## Negotiation Engine
Use a simple FastAPI backend in `backend/main.py` so the model API key is never exposed in browser code.

The frontend should be a Vite + React + TypeScript single-page app.

### Required Environment Variables
Use:

- `OPENAI_API_KEY`
- optional: `NEGOTIATION_MODEL`

If `NEGOTIATION_MODEL` is not provided, default to `gpt-4.1-mini`.

### Negotiation Start Message
Each match begins with this exact user message to the buyer agent:

`Let's begin the negotiation. Please make your initial offer and inquiries.`

### Turn Order
Each match should run like this:

1. Build the buyer system prompt using the buyer strategy.
2. Build the seller system prompt using the seller strategy.
3. Send the start message to the buyer agent.
4. The buyer replies first.
5. Pass the buyer's message to the seller agent.
6. Alternate turns between buyer and seller.
7. Stop when either side accepts or when 12 total rounds have been reached.

### Acceptance Rule
An agreement is reached when an agent says:

`I accept the offer of $X.`

Parse the dollar amount robustly so values like `$20,000` work correctly.

If no one accepts within 12 rounds, the match ends with no agreement.

## Scoring
For an agreed price:

- buyer surplus = `22000 - agreed_price`
- seller surplus = `agreed_price - 18000`

If there is no agreement:

- buyer surplus = `0`
- seller surplus = `0`

## Seeded Opponent Strategies
To make the app immediately usable, ship with a small fixed opponent pool.

Use these seeded strategies:

### Emaad
Buyer strategy:

`Start with a firm low anchor well below the KBB range. Make small, slow concessions, and push hard on the walk-away threat. Aim to close as low as possible and do not match midpoints unless it is the final round.`

Seller strategy:

`Open with a very high anchor above the KBB range. Concede reluctantly in small steps, and emphasize scarcity and demand. Hold the line near the top of the range and only move meaningfully if the deal is close to closing.`

### Alex
Buyer strategy:

`Offer a deal a few thousand under the KBB range and go up by a few hundred dollars as needed with the aim of closing as low as possible.`

Seller strategy:

`Start with an offer that’s a few thousand dollars above the midpoint of the blue book value range. Go down by $500–$1000 each round if necessary. Aim to close a deal as far above the trade-in price as possible.`

### Tournament Rule
When the user submits a `Buyer` strategy:

- run one match against each seeded seller strategy

When the user submits a `Seller` strategy:

- run one match against each seeded buyer strategy

The UI should show one result block per opponent.

## Results View
After submission completes, open a modal or results panel.

Show:

- your role
- opponent role
- total surplus across all matches
- one result block per tournament

Each tournament block should show:

- opponent name
- agreed price, or `N/A` if no deal
- your surplus
- opponent surplus
- transcript

### Transcript Format
Render the transcript as a chat-like log with role-prefixed messages, for example:

- `buyer`
- `seller`

The transcript should preserve message order and round-by-round content.

## Leaderboard
Include a simple leaderboard in the right column.

Minimal acceptable behavior:

- seed the leaderboard with `Emaad` and `Alex`, each starting at `0` total surplus
- add or update a local `You` entry after each submission
- store leaderboard data in `localStorage`
- sort by total surplus descending
- show only the top 10 rows

The leaderboard does **not** need to be shared across users.

## Visual Direction
The app should use a minimal aesthetic with:

- restrained black / slate / gray palette
- warm paper-like page background
- white content surfaces
- strong monospace influence for terminal content
- minimal accent color usage
- sharp edges instead of soft glassy UI

Do not make it glossy, playful, or app-store-generic.

## Interaction Details
Required interaction behavior:

- disable `Submit` while a run is in progress
- show a visible loading state while negotiations are running
- prevent empty submission
- keep the UI responsive on mobile
- show user-friendly error messaging if the backend call fails

## Minimal API Shape
Implement this route in the FastAPI backend:

- `POST /api/negotiate`

`POST /api/negotiate` accepts:

```json
{
  "role": "buyer",
  "strategy": "..."
}
```

and returns enough data for:

- total surplus
- per-opponent results
- agreed prices
- per-match transcripts

The frontend should use the returned tournament data to update the local leaderboard in `localStorage`.

## Out Of Scope
Do not spend time on:

- authentication
- database-backed strategy pools
- matchmaking against real users
- async queues
- shareable URLs
- admin tooling
- analytics

## Acceptance Criteria
The spec is satisfied when:

- the page presents a coherent terminal-like negotiation interface matching the visual direction above
- a user can choose buyer or seller
- a user can enter a strategy and inspect the active system prompt
- submission runs a full tournament against the seeded opposite-side strategies
- each match uses the negotiation rules above
- the app computes and displays surplus correctly
- transcripts are visible in the results view
- a simple local leaderboard appears in the right rail
- the implementation is self-contained and usable without adding extra product decisions

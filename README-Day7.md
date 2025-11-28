# Day 7 — Food & Grocery Ordering Voice Agent

A voice shopping assistant that understands items and quantities, supports "ingredients for X" requests using recipe bundles, manages a cart, and places orders to JSON. Built with LiveKit Agents, Murf Falcon TTS, Deepgram Nova-3 STT, and Gemini 2.5 Flash.

## Overview

- Persona: Friendly food & grocery ordering assistant for a fictional store
- Capabilities:
  - Loads a catalog JSON with items and recipes
  - Adds/removes/updates items in a conversational cart
  - Handles bundled "ingredients for X" requests (e.g., pasta for two)
  - Lists cart and calculates totals
  - Places the order and persists to JSON

## Architecture

- Backend: `backend/src/agent.py`
  - Agent: `GroceryAgent` at backend/src/agent.py:538
  - Tools:
    - Add item: backend/src/agent.py:580-594
    - Remove item: backend/src/agent.py:596-602
    - Update quantity: backend/src/agent.py:604-612
    - List cart: backend/src/agent.py:615-625
    - Add recipe: backend/src/agent.py:627-643
    - Place order: backend/src/agent.py:645-674
  - Startup: session starts `GroceryAgent` at backend/src/agent.py:834-841
  - Worker name: `grocery-agent` at backend/src/agent.py:849-854
- Content
  - Catalog: `shared-data/day7_catalog.json` (items + recipes)
- Frontend
  - Dispatches to `grocery-agent` via `frontend/app-config.ts:40`
  - Token endpoint reads LiveKit creds in `frontend/app/api/connection-details/route.ts`

## Requirements

- Backend env: `MURF_API_KEY`, `GOOGLE_API_KEY`, `DEEPGRAM_API_KEY`, `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`
- Frontend env: `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`

## Setup

```bash
# Backend
cd backend
python src\agent.py dev

# Frontend
cd frontend
pnpm dev
# Open http://localhost:3000/
```

## Use

- Add items: "Add Bread two", "Add Milk 1L".
- Bundles: "Ingredients for a peanut butter sandwich", "Pasta for two".
- Cart: "What’s in my cart?", "Remove Bread", "Update Bread to three".
- Place order: "Place my order — name Sam, address MG Road".
- Check saved orders: `backend/orders/order_<timestamp>.json`.

## Notes

- Prompt follows best practices: clear identity, goals, tool usage, guardrails, concise spoken responses.
- All persistence is via JSON; demo-only flow suitable for prototyping.
- Advanced goal (optional): Add order tracking statuses and “where is my order?” responses.

## Resources

- Prompting: https://docs.livekit.io/agents/build/prompting/
- Tools: https://docs.livekit.io/agents/build/tools/


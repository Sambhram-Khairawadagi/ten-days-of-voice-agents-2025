# Day 9 — E-commerce Agent (ACP-Inspired)

A voice-driven shopping assistant that follows a lite version of the Agentic Commerce Protocol (ACP). The agent keeps conversation separate from commerce logic, uses structured tools to browse a catalog and create orders, and persists orders to JSON.

## Overview

- Persona: Voice shopping assistant with ACP-inspired flow
- Capabilities:
  - Browse and filter a small product catalog by voice
  - Summarize items with name and price, enumerated 1..N
  - Place orders using natural references (index, name, color/size, category)
  - Confirm orders with item names, quantities, total, and currency
  - Read back the most recent order

## Architecture

- Backend: `backend/src/agent.py`
  - Agent: `EcommerceAgent` starts at backend/src/agent.py:724
  - List products tool: backend/src/agent.py:802
  - Create order tool: backend/src/agent.py:863
  - Last order tool: backend/src/agent.py:931
  - Session starts `EcommerceAgent`: backend/src/agent.py:999-1006
  - Worker `agent_name`: `ecommerce-agent`: backend/src/agent.py:1013-1019
  - Catalog loaded into session: backend/src/agent.py:959-963
- Catalog: `shared-data/day9_catalog.json`
- Orders: persisted as JSON in `backend/orders/order-<timestamp>.json`

## Data Model

- Product: `id`, `name`, `description`, `price`, `currency`, `category`, optional `color`, `size`
- Order: `id`, `items` (product_id + quantity + price), `total`, `currency`, `created_at`

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

- Frontend agent dispatch: `frontend/app-config.ts` uses `agentName: 'ecommerce-agent'`
- Env vars: `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`, `DEEPGRAM_API_KEY`, `GOOGLE_API_KEY`, `MURF_API_KEY`

## Use

- Browse: “Show me all coffee mugs”, “Any t-shirts under 1000?”
- Buy: “I’ll buy the second hoodie you mentioned, in size M”, “Buy the ceramic coffee mug”
- Check: “What did I just buy?”

## Implementation Notes

- Listing saves `userdata['acp']['last']` with enumerated summary for ordinal selection.
- `create_order` accepts a single `payload` object and resolves from last results or catalog when `product_id` is missing.
- Quantity parsing supports words like “one”, “two”, “a”, “an”.

## Resources

- Prompting: https://docs.livekit.io/agents/build/prompting/
- Tools: https://docs.livekit.io/agents/build/tools/

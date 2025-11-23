# Day 2 — Coffee Shop Barista Agent

Turn the starter agent into a friendly barista for "Delta Coffee Shop" that takes voice orders, asks clarifying questions, and saves the final order to JSON. Includes a live HTML drink visual in the frontend.

## Features

- Conversational barista persona with warm, concise responses
- Order state management: `customer_name`, `drink`, `size`, `milk`, `extras`
- Clarifying questions until required fields are set
- Order confirmation and JSON persistence for fulfillment
- Latest order API for the UI
- HTML-based drink image that changes with order details

## Backend Implementation

- Order data model: `backend/src/agent.py:31-39`
- Missing fields helper: `backend/src/agent.py:40-50`
- Tools to set fields: `backend/src/agent.py:59-97`
- Finalize and save order to JSON: `backend/src/agent.py:99-117`
- Pipeline setup: STT `nova-3`, LLM `gemini-2.5-flash`, TTS Murf Falcon
  - STT: `backend/src/agent.py:152`
  - LLM: `backend/src/agent.py:155-157`
  - TTS: `backend/src/agent.py:160-165`
- Initialize session state: `backend/src/agent.py:175`
- Worker registration (Cloud dispatch): `backend/src/agent.py:224-231`

Orders are saved to `backend/orders/order_<timestamp>_<name>.json`.

## Frontend Implementation

- Latest order endpoint: `frontend/app/api/orders/latest/route.ts:1`
- Order Visual component: `frontend/components/app/order-visual.tsx:1`
- Integrated in session view: `frontend/components/app/session-view.tsx:95-118`

The visual renders an HTML cup:
- Size determines cup dimensions
- Drink type determines color
- Extras including "whip" add a whipped-cream shape on top

## Run

Option A — LiveKit Server

```bash
livekit-server --dev

# Terminal 1
cd backend
python src\agent.py dev

# Terminal 2
cd frontend
pnpm dev
```

Option B — LiveKit Cloud

```bash
# Ensure LIVEKIT_URL/API keys are set in both .env.local files

# Terminal 1
cd backend
python src\agent.py dev

# Terminal 2
cd frontend
pnpm dev
```

Open `http://localhost:3000/` and click `Start call`.

## Test

- Speak a complete order: “Medium latte with oat milk and whipped cream”
- Confirm transcript updates in the chat panel
- See the drink visual update under the agent tile
- Fetch latest order JSON: `http://localhost:3000/api/orders/latest`
- Verify file in `backend/orders/`

## Environment

- Backend `.env.local`: `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`, `MURF_API_KEY`, `GOOGLE_API_KEY`, `DEEPGRAM_API_KEY`
- Frontend `.env.local`: same LiveKit credentials

## Troubleshooting

- Agent stalls or doesn’t respond
  - Ensure backend worker registers with a stable `agent_name` and frontend requests that name: `backend/src/agent.py:224-231`
- Frontend build/type errors (Motion v12)
  - Transitions use `easing` instead of `ease`; code updated in `chat-transcript.tsx`, `preconnect-message.tsx`, `view-controller.tsx`, `tile-layout.tsx`, `chat-input.tsx`
- API returns null order
  - Place and finalize an order to create a JSON file under `backend/orders`

## Advanced Challenge

Optional HTML-based beverage image or a simple order receipt. Current implementation uses a cup visual. You can replace `OrderVisual` to render a textual HTML receipt instead.

Resources:
- Text Streams: https://docs.livekit.io/home/client/data/text-streams/
- RPC: https://docs.livekit.io/home/client/data/rpc/

## Suggested Post

- Short demo clip speaking an order and showing the JSON + visual
- Hashtags: `#TenDaysOfVoiceAgents #VoiceAI #LiveKit #MurfAI #Deepgram #Gemini #TypeScript #Python`


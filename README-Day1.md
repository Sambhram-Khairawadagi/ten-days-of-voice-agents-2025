# Day 1 — Starter Voice Agent

This guide documents the Day 1 challenge deliverable: a fully working starter voice agent using LiveKit Agents with Murf Falcon TTS, Deepgram Nova-3 STT, and Google Gemini 2.5 Flash.

## Features

- Real-time voice conversation with turn detection
- Ultra-fast, natural speech via Murf Falcon TTS
- Accurate speech recognition via Deepgram Nova-3
- Responsive reasoning via Gemini 2.5 Flash
- Works locally with LiveKit Server or LiveKit Cloud
- Beautiful React/Next.js UI with transcript and call controls

## Architecture

- Backend voice pipeline: `backend/src/agent.py`
  - STT: `deepgram.STT(model="nova-3")` at `backend/src/agent.py:152`
  - LLM: `google.LLM(model="gemini-2.5-flash")` at `backend/src/agent.py:155-157`
  - TTS: `murf.TTS(...)` at `backend/src/agent.py:160-165`
  - Turn detection: `MultilingualModel()` at `backend/src/agent.py:168`
  - Session start and connect at `backend/src/agent.py:211-222`
- Frontend token route: `frontend/app/api/connection-details/route.ts`
- Frontend session UI: `frontend/components/app/session-view.tsx`, transcript: `frontend/components/app/chat-transcript.tsx`

## Prerequisites

- Python 3.9+
- Node.js 18+ and `pnpm`
- LiveKit Server or a LiveKit Cloud project
- API keys: `MURF_API_KEY`, `GOOGLE_API_KEY`, `DEEPGRAM_API_KEY`

## Configure Environment

1) Backend

```bash
cd backend
copy .env.example .env.local   # Windows
# Set:
# LIVEKIT_URL, LIVEKIT_API_KEY, LIVEKIT_API_SECRET
# MURF_API_KEY, GOOGLE_API_KEY, DEEPGRAM_API_KEY
```

2) Frontend

```bash
cd frontend
copy .env.example .env.local   # Windows
# Set the same LiveKit credentials used for the backend
```

## Run Locally

Option A — LiveKit Server dev

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
# No local server needed. Ensure LIVEKIT_URL and keys are set in both .env.local files

# Terminal 1
cd backend
python src\agent.py dev

# Terminal 2
cd frontend
pnpm dev
```

Open `http://localhost:3000/` and click `Start call`.

## Verify

- Frontend lint and build

```bash
cd frontend
pnpm lint
pnpm build
```

- Backend logs show session start and metrics

- Speak into the mic and watch transcript messages update in the UI

## Troubleshooting

- No audio or connection
  - Check `.env.local` values (URL and keys) on both backend and frontend
  - Ensure microphone permission is allowed in the browser
- Worker not dispatched on Cloud
  - Ensure the backend agent registers a valid `agent_name` at `backend/src/agent.py:224-231`
  - Ensure the frontend requests the same agent name via its config
- Build errors (frontend)
  - Use `pnpm install` and `pnpm build`; if Motion version issues appear, ensure transitions use `easing` not `ease`

## What To Post

- Short demo clip: connecting, speaking, hearing the agent respond
- Mention the stack and a note on Murf Falcon responsiveness
- Suggested hashtags: `#TenDaysOfVoiceAgents #VoiceAI #LiveKit #MurfAI #Deepgram #Gemini #TypeScript #Python`


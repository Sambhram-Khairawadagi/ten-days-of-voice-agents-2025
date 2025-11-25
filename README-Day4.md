# Day 4 — Active Recall Voice Coach

Build a voice tutor with three complementary learning modes: learn, quiz, and teach_back — each with a distinct Murf Falcon voice. The agent runs on LiveKit Agents with Deepgram Nova-3 STT and Gemini 2.5 Flash.

## Overview

- Modes
  - learn — explains a concept (Murf voice: Matthew)
  - quiz — asks you questions (Murf voice: Alicia)
  - teach_back — you explain the concept back; agent gives concise feedback (Murf voice: Ken)
- Content is loaded from `shared-data/day4_tutor_content.json` (concepts, summaries, sample questions)
- Teach-back feedback entries are persisted in `backend/tutor/mastery_log.json`

## Architecture

- Backend: `backend/src/agent.py`
  - Coordinator agent: greets, sets concept, switches modes using tools
  - Mode agents: `LearnAgent`, `QuizAgent`, `TeachBackAgent` with per-agent Murf Falcon voices
  - Teach-back evaluation tool logs progress
  - Key locations
    - Coordinator tools: start_learn `backend/src/agent.py:312-334`, start_quiz `backend/src/agent.py:336-358`, start_teach_back `backend/src/agent.py:360-383`
    - Mode agents: `LearnAgent` (Matthew) `backend/src/agent.py:270-274`, `QuizAgent` (Alicia) `backend/src/agent.py:276-280`, `TeachBackAgent` (Ken) `backend/src/agent.py:282-286`
    - Teach-back feedback tool: `backend/src/agent.py:235-268`
- Frontend: `frontend/app-config.ts`
  - Dispatches to agent name `active-recall-coach` (`frontend/app-config.ts:40`)

## Requirements

- Environment variables
  - Backend: `MURF_API_KEY`, `GOOGLE_API_KEY`, `DEEPGRAM_API_KEY`, `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`
  - Frontend: `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`
- Files
  - Tutor content: `shared-data/day4_tutor_content.json`
  - Mastery log: `backend/tutor/mastery_log.json` (auto-created)

## Setup

1) Backend env

```bash
cd backend
copy .env.local.example .env.local  # if present
# Fill in MURF_API_KEY, GOOGLE_API_KEY, DEEPGRAM_API_KEY, LIVEKIT_*
```

2) Frontend env

```bash
cd frontend
copy .env.local.example .env.local  # if present
# Fill in LIVEKIT_URL, LIVEKIT_API_KEY, LIVEKIT_API_SECRET
```

## Run

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

- Say one of:
  - “Let’s learn variables”
  - “Quiz me on loops”
  - “I’ll teach back variables”
- Switch modes anytime:
  - “Switch to teach back”
  - “Quiz mode”
- Check progress: `backend/tutor/mastery_log.json`

## Troubleshooting

- Voice not changing between modes
  - Voices are set per agent (Matthew/Alicia/Ken). If your Murf plan doesn’t include a voice, Murf may fall back to default. Verify `MURF_API_KEY` and consider mapping to supported voices.
- “Internal error” on mode switch
  - Ensure `shared-data/day4_tutor_content.json` exists and contains valid concepts (e.g., `variables`, `loops`).
  - Frontend `agentName` should be `active-recall-coach` (`frontend/app-config.ts:40`).
  - Restart backend and frontend, then refresh.
- Service unavailable
  - Restart servers and re-open `http://localhost:3000/`.

## Notes

- This challenge demonstrates agent handoffs and per-agent TTS configuration to deliver distinct voices per learning mode.
- Teach-back logs provide simple qualitative feedback markers (`needs work`, `developing`, `solid`) saved with timestamps and concept IDs.


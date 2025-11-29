# Day 8 — Voice Game Master (D&D-Style Adventure)

Turn your voice agent into a Game Master (GM) that runs a single-universe, interactive adventure. The GM describes scenes, remembers your choices via chat history, and ends each turn with “What do you do?”. Built with LiveKit Agents, Murf Falcon TTS, Deepgram Nova-3 STT, and Gemini 2.5 Flash.

## Overview

- Persona: GM for a fantasy world (dragons, magic), dramatic tone, concise voice output
- Capabilities:
  - Describes scenes and prompts for player action each turn
  - Maintains continuity using conversation history (characters, locations, decisions)
  - Supports a “Restart story” tool to begin a fresh adventure
- Basic UI:
  - Shows GM text and player’s transcribed speech
  - Adds a “Restart story” button

## Architecture

- Backend: `backend/src/agent.py`
  - Agent: `GameMasterAgent` at backend/src/agent.py:676-699
  - Tool: `restart_story()` resets GM state at backend/src/agent.py:692-699
  - Session start uses GM: backend/src/agent.py:862-869
  - Worker name: `game-master-agent` at backend/src/agent.py:876-882
- Frontend
  - Agent dispatch: `frontend/app-config.ts:40` (uses `game-master-agent`)
  - Transcript UI: `frontend/components/app/session-view.tsx:104-109`
  - Transcript toggle + Restart button: `frontend/components/livekit/agent-control-bar/agent-control-bar.tsx:152-160,165-176`
  - Chat and transcriptions merged: `frontend/hooks/useChatMessages.ts:18-31`

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

- Start the call and say “Begin the adventure.”
- Respond to the GM’s scene prompts with actions (e.g., “I draw my sword,” “Search the ruins”).
- Click “RESTART STORY” to reset and start a fresh adventure.

## Notes

- Follows best practices for prompting: explicit identity, goals, guardrails, and concise voice output.
- Continuity relies on the conversation transcript; no external world model is required for MVP.
- Extendable: you can add a JSON world model and more tools for a stateful RPG engine.

## Resources

- Prompting: https://docs.livekit.io/agents/build/prompting/
- Tools: https://docs.livekit.io/agents/build/tools/


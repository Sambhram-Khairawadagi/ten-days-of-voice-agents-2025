# Day 3 — Health & Wellness Voice Companion

A grounded, supportive voice agent that performs a daily check‑in, stores each session in JSON, and gently references past entries.

## Features

- Daily check‑ins via voice: mood, energy, and 1–3 practical objectives
- Small, actionable reflections; non‑diagnostic guidance
- Brief recap and confirmation
- JSON persistence in `backend/wellness/wellness_log.json`
- Uses prior entries to inform the next conversation

## Behavior

- Ask: “How are you feeling today?”, “What’s your energy like?”, “Anything stressing you out?”
- Ask: “What are 1–3 things you’d like to get done today?”
- Offer simple reflections: break goals into smaller steps, take short breaks, 5‑minute walk
- Recap: mood summary + objectives; confirm “Does this sound right?”
- Avoid diagnosis or medical claims; supportive companion only

## Implementation

- Prompt and tools: `backend/src/agent.py:51-54`, `backend/src/agent.py:57-117`
  - `set_mood`, `set_energy`, `add_objective`, `clear_objectives`, `get_recent_summary`, `finalize_checkin`
- Persistence and reference:
  - Read history at session start: `backend/src/agent.py:175-190`
  - Append entry to JSON: `backend/src/agent.py:102-117`
  - JSON path: `backend/wellness/wellness_log.json`
- Voice pipeline (STT/LLM/TTS): `backend/src/agent.py:152-165`
- Agent dispatch name:
  - Backend worker: `backend/src/agent.py:241-246` (defaults to `wellness-companion`)
  - Frontend app config: `frontend/app-config.ts:39-41` (`agentName: 'wellness-companion'`)

## Data Schema

Each entry in `wellness_log.json`:

```json
{
  "timestamp": 1763919000,
  "mood": "okay",
  "energy": "8/10",
  "objectives": ["reply to two emails", "10-minute walk"],
  "summary": "Feeling capable; plan small steps and a short walk."
}
```

## Run

Local server:

```bash
# Backend
cd backend
python src\agent.py dev

# Frontend
cd frontend
pnpm dev
# Open http://localhost:3000/
```

LiveKit Cloud:

```bash
# Ensure LIVEKIT_URL / keys in backend/.env.local and frontend/.env.local
cd backend && python src\agent.py dev
cd frontend && pnpm dev
```

## Test

- Start call, allow microphone
- Provide mood, energy, and 1–3 objectives
- Agent offers reflections, then a recap; upon confirmation it saves the entry
- Verify file: `backend/wellness/wellness_log.json`
- On a new session, the agent references the last entry (via `get_recent_summary`)

## Resources

- Tools: https://docs.livekit.io/agents/build/tools/
- State passing: https://docs.livekit.io/agents/build/agents-handoffs/#passing-state
- Tasks: https://docs.livekit.io/agents/build/tasks/
- Drive‑thru example: https://github.com/livekit/agents/blob/main/examples/drive-thru/agent.py

## Notes

- Keep advice small and grounded; avoid medical claims
- If you prefer a UI panel for the last entry, add a frontend component that reads `wellness_log.json` and renders mood/energy/objectives under the agent tile

## Expanded Details

- Conversation flow
  - Greets and optionally references last entry via tool
  - Collects `mood` and `energy`
  - Collects up to three `objectives` (can add or clear)
  - Offers short, practical reflections
  - Recaps and asks for confirmation
  - Persists entry, then ends the check‑in
- Tool definitions
  - `set_mood`: `backend/src/agent.py:57`
  - `set_energy`: `backend/src/agent.py:63`
  - `add_objective`: `backend/src/agent.py:69`
  - `clear_objectives`: `backend/src/agent.py:79`
  - `get_recent_summary`: `backend/src/agent.py:85`
  - `finalize_checkin`: `backend/src/agent.py:96`
- Prompt and behavior
  - System prompt: `backend/src/agent.py:53`
  - Uses grounded tone; avoids diagnosis; focuses on small, actionable steps
- Persistence model
  - Reads existing log on session start: `backend/src/agent.py:175-190`
  - Appends new entry on finalize: `backend/src/agent.py:101-117`
  - Human‑readable JSON array at `backend/wellness/wellness_log.json`
- Voice pipeline
  - STT Deepgram Nova‑3: `backend/src/agent.py:152`
  - LLM Gemini 2.5 Flash: `backend/src/agent.py:155-157`
  - TTS Murf Falcon: `backend/src/agent.py:160-165`
  - Turn detection Multilingual: `backend/src/agent.py:168`
- Metrics
  - Usage collection and logging: `backend/src/agent.py:202-216`
- Reset or migrate data
  - To reset history, delete `backend/wellness/wellness_log.json` and restart backend
  - To migrate, transform the array entries to a new schema with a one‑off script
- Limitations
  - No medical advice; not a clinician
  - References prior session only if history exists
  - Single shared log file; no per‑user separation in the current setup
- Future improvements
  - Per‑identity logs (by room/participant)
  - UI panel for latest wellness entry
  - Weekly summaries and gentle nudges using scheduled tasks
  - Optional Text Streams/RPC to push UI updates directly

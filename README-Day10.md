# Day 10 — Voice Improv Battle

Build a voice-first improv game show. The AI hosts “Improv Battle”, sets short-form scenarios, listens to your performance, reacts with varied tone, and advances rounds. Single player (primary goal) with simple backend state.

## Overview

- Host persona: high-energy, witty, clear about rules; respectful yet honest reactions
- Flow: intro → N rounds (3 by default). Each round: set scenario → you improvise → host reacts → next round → closing summary
- State: per-session `improv` object tracks player name, round counters, and reactions

## Architecture

- Backend: `backend/src/agent.py`
  - Agent: `ImprovHostAgent` at backend/src/agent.py:801
  - Tools:
    - `set_player_name(name?)`: backend/src/agent.py:834
    - `start_improv(player_name?, max_rounds?)`: backend/src/agent.py:842
    - `next_scenario()`: backend/src/agent.py:853
    - `end_scene(reaction?)`: backend/src/agent.py:869
    - `stop_improv()`: backend/src/agent.py:880
    - `get_state()`: backend/src/agent.py:887
  - Session starts `ImprovHostAgent`: backend/src/agent.py:1240-1247
  - Worker name: `improv-host-agent`: backend/src/agent.py:1253-1260
  - State initialization: `improv` added in `session.userdata`: backend/src/agent.py:1196-1204
- Frontend
  - Agent dispatch: `frontend/app-config.ts:40` uses `improv-host-agent`
  - Join UI: `frontend/components/livekit/agent-control-bar/agent-control-bar.tsx:183-191` adds Name input and “START IMPROV BATTLE” button

## State Model

```json
{
  "player_name": null,
  "current_round": 0,
  "max_rounds": 3,
  "rounds": [ { "scenario": "...", "host_reaction": "..." } ],
  "phase": "intro"
}
```

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

- Env vars required for voice pipeline: `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`, `DEEPGRAM_API_KEY`, `GOOGLE_API_KEY`, `MURF_API_KEY`

## Use

- Enter Name and click “START IMPROV BATTLE”
- Perform when the host sets the scene; say “End scene” to finish a round
- Host reacts with mixed tone (supportive, neutral, mildly critical), then moves on
- After 3 rounds, host gives a closing summary and ends the show
- Say “stop game” to exit early

## Scenarios

- Time-travelling tour guide explaining smartphones to the 1800s
- Restaurant waiter whose order escaped the kitchen
- Customer returning a cursed object to a skeptical shop owner
- Barista revealing a latte is a portal to another dimension
- Museum guard hearing paintings whisper career advice

## Notes

- Host uses short, voice-friendly lines, ends prompts with clear cues to begin or continue
- Reactions are varied yet respectful; constructive critique is encouraged
- State is stored server-side and updated via tools

## Resources

- Prompting: https://docs.livekit.io/agents/build/prompting/
- Tools: https://docs.livekit.io/agents/build/tools/
- Voice flow callbacks: https://docs.livekit.io/agents/build/nodes/#on_user_turn_completed
- Exit handling: https://docs.livekit.io/agents/build/nodes/#on-exit
- Transcription: https://docs.livekit.io/agents/build/nodes/#transcription-node

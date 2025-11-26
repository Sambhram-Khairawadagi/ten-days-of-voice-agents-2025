# Day 6 — Fraud Alert Voice Agent (State Bank of India)

Build a voice fraud-alert agent that safely verifies a customer, reads suspicious transaction details, asks if the transaction is legitimate, and updates a "database" entry with the outcome. Uses LiveKit Agents, Murf Falcon TTS, Deepgram Nova-3 STT, and Gemini 2.5 Flash.

## Overview

- Persona: Fraud department agent for State Bank of India (SBI)
- Capabilities:
  - Loads a fraud case from a JSON database after getting the user’s name
  - Performs safe verification using a non-sensitive security question
  - Reads suspicious transaction details (merchant, amount, masked card ending, time, location, category, source)
  - Asks if it’s legitimate; marks as `safe` or `fraudulent`
  - Writes updated status and a short note back to the database
- Safety: No full card numbers, PINs, passwords, or sensitive credentials — demo/sandbox-only

## Architecture

- Backend: `backend/src/agent.py`
  - Fraud agent: `FraudAgent` at backend/src/agent.py:329-346
  - Tools:
    - Load case: `load_case(user_name)` at backend/src/agent.py:348-358
    - Verify: `verify_customer(answer)` at backend/src/agent.py:360-368
    - Read suspicious: `read_suspicious()` at backend/src/agent.py:370-383
    - Mark decision: `mark_case(decision)` at backend/src/agent.py:385-394
    - Finalize + persist: `finalize_case(note?)` at backend/src/agent.py:397-435
  - Agent startup: `entrypoint()` starts `FraudAgent` at backend/src/agent.py:673-681
  - Worker name: `fraud-agent` at backend/src/agent.py:688-694
- Content and persistence
  - Input cases: `shared-data/day6_fraud_cases.json`
  - Runtime copy: `backend/fraud/fraud_cases.json`
  - Outcome log: `backend/fraud/fraud_log.json`
- Frontend
  - Dispatches to `fraud-agent` at `frontend/app-config.ts:40`

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

- Start the call and say your name (e.g., “My name is John”).
- Answer the security question (e.g., “blue”).
- Agent reads suspicious transaction; answer “yes” or “no” to legitimacy.
- Agent summarizes outcome and persists the result.
- Check files:
  - Updated database: `backend/fraud/fraud_cases.json`
  - Outcomes log: `backend/fraud/fraud_log.json`

## Notes

- This is a demo — all data is fake and non-sensitive.
- Answers and actions are mock; use for prototyping and demos only.
- Advanced goal: telephony via LiveKit Telephony and DTMF (optional extension).

## Resources

- Prompting: https://docs.livekit.io/agents/build/prompting/
- Tools: https://docs.livekit.io/agents/build/tools/
- Python SQLite: https://www.geeksforgeeks.org/python/python-sqlite/
- MongoDB with Python: https://www.mongodb.com/resources/languages/python


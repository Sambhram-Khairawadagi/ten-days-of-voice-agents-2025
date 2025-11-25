# Day 5 — Sales Development Representative (SDR) Voice Agent

Build a voice SDR that answers company FAQs, collects lead details conversationally, and stores a summary at the end of the call. Built with LiveKit Agents, Murf Falcon TTS, Deepgram Nova-3 STT, and Gemini 2.5 Flash.

## Overview

- Persona: SDR for a chosen Indian company (sample: Zoho)
- Capabilities:
  - Answers product/company/pricing questions from prepared FAQ content
  - Collects lead fields: name, company, email, role, use_case, team_size, timeline
  - Detects end-of-call and saves a verbal summary plus JSON log

## Architecture

- Backend: `backend/src/agent.py`
  - Lead model: `SDRLead` at backend/src/agent.py:292-299
  - SDR agent: `SDRAgent` at backend/src/agent.py:300-392
    - FAQ answer tool: `answer_question(query)` at backend/src/agent.py:321-336
    - Lead field tool: `set_lead_field(field, value)` at backend/src/agent.py:338-355
    - Finalize tool: `finalize_lead(recap?)` at backend/src/agent.py:356-392
  - Startup agent: `entrypoint()` starts `SDRAgent` at backend/src/agent.py:430-437
  - Worker name: `sdr-agent` at backend/src/agent.py:444-450
- Content
  - `shared-data/day5_sdr_content.json` (company info + FAQs)
- Frontend
  - Dispatches to `sdr-agent` at frontend/app-config.ts:40

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

- Ask FAQs:
  - “What does your product do?”
  - “Do you have a free tier?”
  - “Who is this for?”
- Provide lead info naturally:
  - “My name is Ravi, company Acme Robotics, email ravi@acme.com, we’re evaluating CRM for a 10-person team, timeline soon.”
- End the call:
  - “That’s all”, “I’m done”, “Thanks” → agent summarizes and stores the lead
- Check logs:
  - `backend/leads/leads_log.json`

## Notes

- Silero VAD is prewarmed for accurate turn detection.
- Answers are grounded in your `day5_sdr_content.json`; the agent avoids inventing details.
- For advanced matching, you can split FAQ content and add similarity search (see LiveKit RAG example), but keyword search fulfills MVP.


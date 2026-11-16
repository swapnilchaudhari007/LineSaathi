# LineSaathi ⚡🎙️

**Hands-free Gemini Live voice agent for electricity linemen on the pole.**
Entry for **Build with Google Gemini** (YourStory × Google Cloud × Hack2skill), challenge: *Voice-Powered Enterprise Knowledge & Decision Agent*.

> *"Haath kaam pe, awaaz Saathi pe."*

## The problem
DISCOM linemen restore power with gloves on and both hands busy. One fault means 3–4 phone calls, paper permit-to-work (PTW) books, and SOPs locked in English PDFs. The result is slow restoration, no audit trail and unsafe "line clear / re-charge" miscommunication. Electricity-related accidents killed 18,000+ people in India in 2023 (NCRB ADSI data, via Prayas Energy Group).

## The solution
A real-time, voice-to-voice agent on the **Gemini Live API (native audio)**:
- Talk in **Marathi, Hindi or Hinglish**, with interruptions allowed, through a **feature-phone call** or a Bluetooth helmet headset
- **Grounded answers** from SOPs, CEA safety rules, OEM manuals and past tickets (cross-lingual RAG), with the source clause cited
- **Real actions**: shutdown PTW, fault ticket, material indent, complaint closure with consumer SMS
- **Voice Safety Gate**: a deterministic PTW state machine the LLM cannot skip, with spoken read-back and operator approval
- **Supervisor Live Board**: open PTWs, faults, latency and task completion

## Architecture
```
Feature phone / BT headset
   │  (Exotel/Twilio SIP media stream → WebSocket)
   ▼
Cloud Run voice gateway (ADK agent, tool router, latency meter)
   ⇅ Gemini Live API on Vertex AI (native audio, function calling, barge-in)
   ├── Vertex AI Search / RAG Engine  → SOPs, CEA regs, manuals, tickets
   ├── BigQuery                       → asset registry, fault history, inventory
   ├── Safety Gate (Firestore)        → REQUESTED → OPERATOR_APPROVED → READ_BACK_OK → LINE_CLEAR → … → CLOSED
   ├── DISCOM OMS / SAP-PM API (mock) → PTW, faults, indents
   └── Pub/Sub → SMS · Looker Studio · Cloud Logging audit
```

## Unit economics
| | |
|---|---|
| Cost per 2.5-min call | ≈ ₹5.4 |
| Run cost per lineman per month | ≈ ₹1,120 |
| SaaS price | ₹1,999 / lineman / month (~44% gross margin) |
| 50-lineman pilot | ≈ ₹56,000 / month |

## Repo contents
- `docs/LineSaathi_Idea_Submission.pdf`: idea deck (12 slides)
- `docs/submission-answers.md`: form answers

## Roadmap
- [x] Idea submission
- [ ] Prototype: Gemini Live voice gateway + mock OMS + Safety Gate + Live Board (due 6 Dec 2026)

## Team
**Swapnil Dinkar Chaudhari**, Senior Software Developer, Thane · [LinkedIn](https://linkedin.com/in/swapnil-chaudhari-533a25105)

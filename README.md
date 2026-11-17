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

## ▶ Try the prototype
**Live:** https://swapnilchaudhari007.github.io/LineSaathi/

- **Live call:** paste a Gemini API key (from [AI Studio](https://aistudio.google.com/apikey); it stays in your browser), allow the mic and talk in Hindi, Marathi or Hinglish. Interrupt any time.
- **Scripted demo:** no key needed. Replays a full fault-to-restore call; every tool call, Safety Gate transition and Live Board update is real code.
- Try: *"DTC-114 pe trip hua hai, history batao"*, *"fuse badalne ka procedure kya hai?"*, *"Diva-11 pe shutdown permit chahiye"*, then press the operator buttons on the board and read the permit number back.

### What the prototype does
| Part | Implementation (`index.html`, no build step) |
|---|---|
| Voice | Gemini Live API over WebSocket (`BidiGenerateContent`), 16 kHz PCM16 mic via AudioWorklet, 24 kHz playback, input/output transcription, barge-in (`interrupted` stops playback) |
| Tools (10) | `get_asset_history`, `search_sop`, `check_stock`, `create_ptw_request`, `get_ptw_status`, `submit_read_back`, `mark_work_done`, `log_fault`, `raise_indent`, `close_complaint` |
| Voice Safety Gate | 7-state PTW machine in code. Operator steps need a human click on the board; lineman steps need a spoken read-back with permit number + feeder. Skips and wrong-actor steps are rejected and counted |
| Grounding | SOP corpus; the cited clause is spoken and shown on the board |
| Live Board | Permits, faults, indents, complaints (+ consumer SMS), time to first audio, tool latency, actions, unsafe skips blocked, audit trail |

Sample assets, SOPs and complaints are synthetic. For production, use ephemeral tokens instead of an API key in the browser.

## Unit economics
| | |
|---|---|
| Cost per 2.5-min call | ≈ ₹5.4 |
| Run cost per lineman per month | ≈ ₹1,120 |
| SaaS price | ₹1,999 / lineman / month (~44% gross margin) |
| 50-lineman pilot | ≈ ₹56,000 / month |

## Repo contents
- `index.html`: the working prototype (GitHub Pages)
- `docs/LineSaathi_Idea_Submission.pdf`: idea deck (12 slides)
- `docs/submission-answers.md`: form answers

## Roadmap
- [x] Idea submission
- [x] Prototype: browser Gemini Live agent + 10 tools + Safety Gate + Live Board
- [ ] Telephony bridge (Exotel → Cloud Run → Gemini Live) and Vertex AI RAG over real SOPs

## Team
**Swapnil Dinkar Chaudhari**, Senior Software Developer, Thane · [LinkedIn](https://linkedin.com/in/swapnil-chaudhari-533a25105)

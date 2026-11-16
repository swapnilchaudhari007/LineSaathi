# LineSaathi Prototype Plan (due 6 Dec 2026)

## Scope
| Module | What it does | Stack |
|---|---|---|
| Voice gateway | Streams 16 kHz PCM between caller and Gemini Live, routes tool calls, measures latency | Python, FastAPI, WebSockets, Cloud Run |
| Gemini Live agent | Native audio-to-audio dialogue, barge-in, Hinglish/Marathi, function calling | Gemini Live API on Vertex AI, ADK |
| Knowledge | Cross-lingual RAG over SOPs, CEA safety regs, manuals, past tickets | Vertex AI Search / RAG Engine |
| Asset data | Transformer/feeder registry, fault history, store stock | BigQuery (seeded sample data) |
| Safety Gate | Deterministic PTW state machine the LLM cannot skip | Firestore + backend validation |
| Mock OMS | PTW, fault ticket, indent, complaint APIs | FastAPI + Firestore |
| Live Board | Open PTWs, faults, latency meter, task completion | React |

## Tools exposed to Gemini
- `get_asset_history(asset_id)`
- `search_sop(query, lang)`
- `check_stock(item)`
- `create_ptw_request(feeder, asset)` (needs read-back + operator approval)
- `log_fault(asset, type, cause, note)`
- `raise_indent(item, qty)`
- `close_complaint(id)` (triggers consumer SMS)

## Safety Gate states
REQUESTED → OPERATOR_APPROVED → READ_BACK_OK → LINE_CLEAR → WORK_DONE → RECHARGE_REQUESTED → CLOSED

## Demo checklist
- [ ] Live call with at least one interruption
- [ ] Hinglish + Marathi with background noise
- [ ] Grounded answer with SOP citation on the board
- [ ] PTW + fault ticket + indent created in mock OMS
- [ ] Latency meter visible (time to first audio, tool ms)

## Milestones
- Week 1: gateway + Gemini Live loop + latency meter
- Week 2: RAG + asset tools + Safety Gate
- Week 3: telephony bridge, Live Board, noise tests, demo video

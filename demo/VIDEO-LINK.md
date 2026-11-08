# Demo Video

## Video Link

> 🎬 **Demo video:** [Watch on LinkedIn](https://lnkd.in/p/dndJ3Rxi)
>
> Suggested title when publishing: **"Hercules AI Receptionist — ElevenLabs Voice Agent + n8n AI Agent + Google Calendar & Sheets"**

The video (approx. **90 seconds**) is the sole source for all documentation in this repository. The screenshots in [`assets/`](../assets/) are frames extracted from it.

> **Honesty note shown in the video itself:** the voices are re-created for the video, but every receptionist reply shown is the live AI Agent's real response, taken from n8n executions **#49, #51 and #54**.

---

## What the Video Shows — Scene by Scene

| Time (approx.) | Section | What you see |
|---|---|---|
| 00:00 – 00:04 | **01 · Website & AI receptionist** | The Hercules Detailing homepage: hero slides, "Talk to book" / "See prices", and the Hercules Receptionist widget — "Books · reschedules · cancels", **Start talking** button, typed-input option, and stats: *24/7 voice booking · <2 min to book · 100% hand wash*. |
| 00:05 – 00:20 | 01 · Services & widget | The service catalogue with durations (Exterior hand wash ~1 hr · Interior deep clean ~2 hr · Paint correction 4–8 hr · Ceramic coating 1 day · Leather & fabric care ~1.5 hr · Wheels & engine bay) and the widget's quick-phrase chips: *"Book a full detail"*, *"What's free tomorrow?"*, *"Reschedule"*, *"Cancel"*. |
| 00:13 – 00:24 | 01/03 · The conversation | A caller asks to book a **Full Detail, Thursday 10 am**, giving a demo email. The receptionist checks the system, finds a new client, and asks for a full name and phone number. The caller ("Sam Carter", demo details) provides them; the agent registers him, checks the real calendar, and offers **Thursday the 22nd, 10:00–12:00**. |
| 00:25 – 00:29 | **02 · Exact n8n workflow** | The full canvas of **"ElevenLabs Receptionist (AI Agent)"** (Published): ElevenLabs Webhook → Normalize Request → AI Agent → Respond to ElevenLabs, with Call Memory, the DeepSeek Chat Model, and all seven tool workflows attached. |
| 00:30 – 00:34 | 02 · Webhook contract | Annotation: the ElevenLabs voice agent calls this webhook with `{ message, conversation_id, caller_phone }`. |
| 00:35 – 00:39 | 02 · The agent | Annotation: *"DeepSeek Chat Model decides what to do. Call Memory keeps context per call (session = conversation_id)."* |
| 00:40 – 00:44 | 02 · The response | Annotation: *"Respond to Webhook — Returns `{ response }` for ElevenLabs to speak."* |
| 00:45 – 00:49 | 02 · Book Event sub-workflow | The opened sub-workflow: **When Executed by Another Workflow → Create an event (Google Calendar) → Append row in sheet (Appointment Log) → Booking Confirmation**, with the note that the seven tools *"read and write Google Calendar and Google Sheets."* |
| 00:50 – 00:54 | 02 · Real executions | Executions panel, real runs, 20 Oct 2026 (PKT): **#49** 22:02:46 · Success · 3.7s — Client Lookup → "This is a new client" · **#51** 22:02:58 · Success · 5.5s — New Client CRM added + Check Availability 2026-10-22 10:00–12:00 +05:00 · **#54** 22:03:11 · Success · 6.5s — Book Event "Full Detail – Sam Carter". Footer: *Model: DeepSeek Chat · Memory: Call Memory (session = conversation_id)*. |
| 00:55 – 01:05 | **03 · Live booking & CRM** | The booking completes: *"You're booked, Sam! Full Detail this Thursday, October 22nd, from 10 am to noon, and a calendar invite has been sent…"* — with the toast *"Book Event ran · Google Calendar event created · Appointment Log row added (execution #54)"*. |
| 01:10 – 01:14 | 03 · Clients | The **Hercules Detailing CRM** (Google Sheets) — `Clients` tab now contains the new client row (email, name, phone). |
| 01:15 – 01:18 | 03 · Appointment Log + Calendar | `Appointment Log` row (ID · Email · Appointment Type · Date · Notes) beside the Google Calendar event **"Full Detail – Sam Carter"**, Thu 22 October 2026, 10:00–12:00 (PKT), status confirmed — the video highlights that the log row and the event share **the same event ID**. |
| 01:19 – 01:20 | 03 · Call Log | `Call Log` tab: Date `2026-10-20 10:04 PM`, a summary of the booking call, Outcome **Booked**. |
| 01:20 – 01:25 | **04 · Overview** | Recap over the site and conversation: *"Hercules understands callers, checks real availability, books, reschedules and cancels, and logs every call."* |
| 01:26 – 01:30 | End card | **"Hercules AI Receptionist — Intelligent Appointment Automation"** · tags: *ElevenLabs voice · n8n AI Agent · Google Calendar · Google Sheets* · "Automatically, around the clock." |

*Timestamps are approximate (±2s), taken from frame sampling of the video file.*

---

## Key Screenshots (in `assets/`)

| File | Source timestamp | Shows |
|---|---|---|
| `../assets/project-demo-thumbnail.png` | ~01:27 | End-card title frame — project name, subtitle, and technology tags |
| `../assets/workflow-overview.png` | ~00:26 | Complete n8n workflow canvas with all nodes and tools |
| `../assets/execution-result.png` | ~00:50 | Real executions #49 / #51 / #54 with tool calls and timings |

---

## What the Video Does NOT Show

- A reschedule or cancellation being performed (the tools exist; only booking is demonstrated).
- Any credential, API-key, or settings screens.
- The agent's system prompt.
- Any failure or error case.

See [PROJECT-LIMITATIONS.md](../PROJECT-LIMITATIONS.md) for the full evidence policy.

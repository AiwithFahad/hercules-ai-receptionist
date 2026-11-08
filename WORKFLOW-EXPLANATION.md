# Workflow Explanation — ElevenLabs Receptionist (AI Agent)

This document explains the n8n automation behind the Hercules AI Receptionist, node by node and tool by tool, **exactly as shown in the demonstration video**. Anything the video does not show is marked **"Not confirmed from video."**

---

## 1. The Main Workflow

**Name (as shown in n8n):** `ElevenLabs Receptionist (AI Agent)` — status **Published**.
A sticky note on the canvas reads: *"ElevenLabs Receptionist — ElevenLabs voice agent calls this webhook."*

The main chain is four nodes:

```
ElevenLabs Webhook ──▶ Normalize Request ──▶ AI Agent (Appointment AI Agent) ──▶ Respond to ElevenLabs
```

### 1.1 ElevenLabs Webhook

| | |
|---|---|
| **Node type** | n8n Webhook node (named "ElevenLabs Webhook") |
| **Purpose** | Entry point. The ElevenLabs conversational agent calls this webhook for every caller message. |
| **Input** | The caller's message plus call metadata. Per the video's on-screen annotation, the payload carries `{ message, conversation_id, caller_phone }`. |
| **Processing** | Receives the HTTP request from ElevenLabs and starts the workflow. The webhook is configured to wait for the workflow's response (the chain ends in a Respond to Webhook node) — the exact response-mode setting is **Not confirmed from video**. |
| **Output** | The raw request, passed to Normalize Request. |
| **Next** | Normalize Request |

### 1.2 Normalize Request

| | |
|---|---|
| **Node type** | Shown with a "set fields"-style icon; the exact node type is **Not confirmed from video** (consistent with an n8n Set/Edit Fields node). |
| **Purpose** | Shapes the incoming ElevenLabs payload into the clean fields the AI Agent expects. |
| **Input** | Raw webhook payload (`message`, `conversation_id`, `caller_phone`). |
| **Processing** | Normalises/renames fields so the agent always receives a consistent structure regardless of payload variations. The exact field mapping is **Not confirmed from video**. |
| **Output** | A normalised request item for the agent. |
| **Next** | AI Agent (Appointment AI Agent) |

### 1.3 AI Agent — "Appointment AI Agent"

| | |
|---|---|
| **Node type** | n8n AI Agent node, **Tools Agent** type |
| **Purpose** | The decision-maker. It understands the caller's intent from the conversation and calls the right tool sub-workflows, in the right order, until it can answer. |
| **Chat model** | **DeepSeek Chat Model** node attached beneath the agent. (Confirmed on the canvas and in the executions panel footer: *"Model: DeepSeek Chat"*. Model parameters such as temperature are **Not confirmed from video**.) |
| **Memory** | **Call Memory** node attached to the agent — *"Memory: Call Memory (session = conversation_id)"*. Each call is one session, keyed by the ElevenLabs `conversation_id`, so the agent remembers earlier turns of the same conversation (e.g. the service and time the caller mentioned before giving their name). The memory backend/store type is **Not confirmed from video**. |
| **System prompt** | **Not confirmed from video** — the prompt text is never shown on screen. Its behaviour is observable from the demo: identify the client first, register new clients, always check availability before offering a slot, confirm with the caller before booking. |
| **Input** | Normalised caller message + conversation history from Call Memory. |
| **Processing** | Reasoning loop: read intent → optionally call one or more tools (Section 2) → use tool results to compose the next reply or the next tool call. In the demo, a single caller turn triggered up to two tool calls (execution #51: New Client CRM, then Check Availability). |
| **Output** | The reply text for the caller. |
| **Next** | Respond to ElevenLabs |

### 1.4 Respond to ElevenLabs

| | |
|---|---|
| **Node type** | n8n **Respond to Webhook** node (named "Respond to ElevenLabs") |
| **Purpose** | Returns the agent's reply to ElevenLabs so it can be spoken to the caller. |
| **Input** | The agent's output text. |
| **Processing** | Per the video's on-screen annotation: *"Respond to Webhook — Returns `{ response }` for ElevenLabs to speak."* |
| **Output** | HTTP response body `{ response }` to ElevenLabs. |
| **Next** | End of execution; ElevenLabs voices the reply and the conversation continues. |

---

## 2. The Seven Tool Sub-Workflows

Each tool is a separate n8n workflow invoked by the agent through an **Execute Workflow ("Call …") tool node**. The video states: *"Its seven tools are sub-workflows that read and write Google Calendar and Google Sheets."*

Only **Book Event** is opened on screen (Section 3 below). For the other six, purpose/input/output are documented from their names, the on-screen execution evidence, and the data stores they are shown to touch — internal node layouts are **Not confirmed from video**.

### 2.1 Call 'Client Lookup'

| | |
|---|---|
| **Purpose** | Check whether the caller already exists in the CRM. |
| **Input** | The caller's email (in the demo, the email the caller states at the start of the conversation). Lookup by phone is possible but **Not confirmed from video**. |
| **Processing** | Searches the `Clients` tab of the "Hercules Detailing CRM" Google Sheet for a matching record. |
| **Output** | The client record if found; otherwise a "new client" result. Execution #49 shows the literal result returned to the agent: *"This is a new client."* |
| **Evidence** | Execution #49 (22:02:46, Success, 3.7s) — `Client Lookup · email: <demo email> → "This is a new client"` |

### 2.2 Call 'New Client CRM'

| | |
|---|---|
| **Purpose** | Register a first-time caller in the CRM so they can be booked. |
| **Input** | Full name, phone number, and email collected during the conversation (demo client: "Sam Carter", a demo 555 phone number, a demo email address). |
| **Processing** | Appends a new row to the `Clients` tab (columns: **Email · Name · Phone** — as seen in Section 03 of the video). |
| **Output** | Confirmation that the client was added. |
| **Evidence** | Execution #51 (22:02:58, Success, 5.5s) — `New Client CRM · Sam Carter · <phone> → added`; the new row is visible in the Clients tab in Section 03. |

### 2.3 Call 'Check Availability'

| | |
|---|---|
| **Purpose** | Verify a requested time slot is actually free before the agent offers it. |
| **Input** | Requested date and time window (demo: 2026-10-22, 10:00–12:00, timezone +05:00). How the window's end is derived from the service duration is **Not confirmed from video**. |
| **Processing** | Reads Google Calendar for the requested window. |
| **Output** | Availability result the agent uses to confirm or adjust the offer. In the demo the slot was free and the agent replied the Full Detail "is available, running until noon." |
| **Evidence** | Execution #51 — `Check Availability · 2026-10-22 10:00 – 12:00 +05:00` |

### 2.4 Call 'Book Event'

| | |
|---|---|
| **Purpose** | Create the confirmed appointment in Google Calendar **and** log it in the CRM in one step. |
| **Input** | Client details, service, and the agreed start/end (demo: "Full Detail – Sam Carter", 2026-10-22T10:00–12:00 +05:00). |
| **Processing** | Detailed step-by-step in Section 3 below (this sub-workflow is shown on screen). |
| **Output** | Booking confirmation returned to the agent, including the created event; the agent then tells the caller they are booked and that a calendar invite has been sent. |
| **Evidence** | Execution #54 (22:03:11, Success, 6.5s) — `Book Event · "Full Detail – Sam Carter" · 2026-10-22T10:00 – 12:00 +05:00`; on-screen toast: *"Book Event ran · Google Calendar event created · Appointment Log row added (execution #54)"*. |

### 2.5 Call 'Lookup Appointment'

| | |
|---|---|
| **Purpose** | Find an existing appointment — the necessary first step before changing or cancelling one. |
| **Input** | Client identity (email/phone) and/or appointment details. Exact lookup keys are **Not confirmed from video**. |
| **Processing** | Reads the `Appointment Log` tab and/or Google Calendar (both stores hold the same event ID). Which store is queried is **Not confirmed from video**. |
| **Output** | Matching appointment record(s) for the agent to act on. |
| **Evidence** | Tool present on the canvas. **Not demonstrated in the video.** |

### 2.6 Call 'Delete Appointment'

| | |
|---|---|
| **Purpose** | Cancel an appointment. |
| **Input** | The appointment/event to cancel (identified via Lookup Appointment). |
| **Processing** | Deletes the Google Calendar event; whether the Appointment Log row is deleted or marked cancelled is **Not confirmed from video**. |
| **Output** | Cancellation result for the agent to confirm to the caller. |
| **Evidence** | Tool present on the canvas; the receptionist widget advertises "cancels" and includes a "Cancel" prompt chip. **Not demonstrated in the video.** |

### 2.7 Call 'Update Appointment'

| | |
|---|---|
| **Purpose** | Reschedule an appointment to a new time. |
| **Input** | The existing appointment plus the new date/time. |
| **Processing** | Updates the Google Calendar event (and, by design symmetry with Book Event, the Appointment Log — the log update is **Not confirmed from video**). Whether availability is re-checked first is **Not confirmed from video**, though the agent has the Check Availability tool available. |
| **Output** | Updated appointment details for the agent to confirm. |
| **Evidence** | Tool present on the canvas; the widget advertises "reschedules" and includes a "Reschedule" prompt chip. **Not demonstrated in the video.** |

---

## 3. Sub-Workflow in Detail: Book Event

This is the one sub-workflow the video opens. On-screen title: *"Sub-workflow · Book Event — Called by the agent as the 'Call Book Event' tool. Real nodes from your instance."*

```
When Executed by Another Workflow ──▶ Create an event (Google Calendar) ──▶ Append row in sheet (Appointment Log) ──▶ Booking Confirmation
```

| Step | Node | What it does |
|---|---|---|
| 1 | **When Executed by Another Workflow** | Sub-workflow trigger. Receives the booking parameters the agent passes when it calls the Book Event tool. |
| 2 | **Create an event** (Google Calendar node) | Creates the appointment event with the client as attendee. Result in the demo: *"Full Detail – Sam Carter"*, Thu 22 Oct 2026, 10:00–12:00 (PKT), status **confirmed**; a calendar invite is sent to the client (per the agent's spoken confirmation). |
| 3 | **Append row in sheet** (Google Sheets node → `Appointment Log` tab) | Logs the booking with columns **ID · Email · Appointment Type · Date · Notes**. The ID written is the **same event ID** as the Calendar event — the video highlights this match explicitly ("Same event ID as the Appointment Log row"), which is what makes later lookup/update/delete reliable. |
| 4 | **Booking Confirmation** | Final node; returns the booking result to the calling agent workflow. Its exact node type is **Not confirmed from video** (icon consistent with a Set node). |

---

## 4. One Conversation, Three Executions (as shown in the video)

The demo conversation maps to the agent's execution history on **20 Oct 2026 (PKT)**:

| Caller turn | Execution | Time (PKT) | Duration | Status | Tool calls |
|---|---|---|---|---|---|
| "Book a Full Detail this Thursday at 10 am; my email is …" | #49 | 22:02:46 | 3.7s | ✅ Success | Client Lookup → new client |
| "My name is Sam Carter and my phone number is …" | #51 | 22:02:58 | 5.5s | ✅ Success | New Client CRM → added; Check Availability 2026-10-22 10:00–12:00 +05:00 |
| "Yes, please book it." | #54 | 22:03:11 | 6.5s | ✅ Success | Book Event → "Full Detail – Sam Carter", 2026-10-22T10:00–12:00 +05:00 |

After execution #54, the results are visible in Section 03 of the video:

- **Clients tab** — the new client row (email, name, phone).
- **Appointment Log tab** — the booking row carrying the shared event ID.
- **Google Calendar** — the confirmed event with the client as attendee.
- **Call Log tab** — Date `2026-10-20 10:04 PM`, a summary of the call (new client booked a Full Detail), Outcome **Booked**.

> Which node/workflow writes the Call Log entry is **Not confirmed from video** — the tab and its contents are shown, but the writing step is never displayed.

---

## 5. Data Stores Referenced

| Store | Tabs / objects | Written by | Read by |
|---|---|---|---|
| Google Sheets — "Hercules Detailing CRM" | `Clients` | New Client CRM | Client Lookup |
| | `Appointment Log` | Book Event (append row) | Lookup Appointment *(inferred — Not confirmed from video)* |
| | `Call Log` | Writer **Not confirmed from video** | Business owner (review) |
| Google Calendar | Appointment events | Book Event (create), Update/Delete Appointment *(not demonstrated)* | Check Availability |

---

*All statements above are drawn from the demonstration video. See [PROJECT-LIMITATIONS.md](PROJECT-LIMITATIONS.md) for the evidence policy.*

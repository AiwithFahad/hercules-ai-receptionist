# Setup Guide — Hercules AI Receptionist

How to recreate the system shown in the demonstration video.

> **Read this first — two kinds of steps appear below:**
> - ✅ **Verified from video** — directly visible on screen (workflow names, node names, payload fields, sheet structure).
> - 🛠 **Suggested reconstruction** — standard n8n/ElevenLabs/Google practice needed to rebuild an equivalent system, but **not shown** in the video. Treat these as guidance, not as a description of the original build.
>
> No API keys, passwords, or credentials appear in this guide. Create your own and store them in n8n's credential manager — never in workflow fields or in this repository.

---

## 1. Required Software & Accounts

| Requirement | Purpose | Verified? |
|---|---|---|
| n8n instance (version **Not confirmed from video**) | Runs the agent workflow + 7 tool sub-workflows | ✅ product shown; version 🛠 |
| ElevenLabs account with a conversational (voice) agent | Voice interface + website widget | ✅ |
| DeepSeek API account | Chat model for the AI Agent | ✅ model shown; account 🛠 |
| Google account | Google Calendar + Google Sheets access | ✅ |
| A website to embed the widget | Caller entry point (demo uses the Hercules Detailing site) | ✅ |

## 2. Data Stores to Prepare 🛠 (structure ✅ verified)

Create a Google Sheets spreadsheet named **"Hercules Detailing CRM"** ✅ with three tabs and these header rows (headers exactly as seen in the video):

**`Clients`** ✅

| Email | Name | Phone |
|---|---|---|

**`Appointment Log`** ✅

| ID | Email | Appointment Type | Date | Notes |
|---|---|---|---|---|

**`Call Log`** ✅

| Date | Summary | Outcome |
|---|---|---|---|

Create (or choose) a **Google Calendar** for the business's appointments ✅. Events created by the system carry the client as attendee, so invites are sent automatically by Google Calendar (the agent's confirmation in the demo states an invite was sent) ✅.

## 3. Credentials (create in n8n — never hard-code) 🛠

| Credential | Used by | Notes |
|---|---|---|
| DeepSeek API key | DeepSeek Chat Model node | ✅ a DeepSeek model is used; the credential type is the standard n8n DeepSeek credential |
| Google OAuth2 (Calendar + Sheets scopes) | All Google nodes in the sub-workflows | One Google credential can serve both APIs |
| ElevenLabs ↔ n8n | Webhook call from the ElevenLabs agent | The video shows no authentication on the webhook; whether any header/token auth is configured is **Not confirmed from video** — adding n8n webhook auth is recommended 🛠 |

## 4. Build the Tool Sub-Workflows

Create seven separate n8n workflows, each starting with an **Execute Workflow Trigger** ("When Executed by Another Workflow") ✅ (trigger name verified on the Book Event canvas). Each returns its result to the calling agent.

| # | Sub-workflow | Build | Verified? |
|---|---|---|---|
| 1 | **Client Lookup** | Sheets read on `Clients`, match by email; return the record or a "new client" result | ✅ behaviour + result text; node layout 🛠 |
| 2 | **New Client CRM** | Sheets append to `Clients` (Email, Name, Phone) | ✅ behaviour + result; layout 🛠 |
| 3 | **Check Availability** | Calendar read for the requested window; return free/busy | ✅ behaviour + window format `+05:00`; layout 🛠 |
| 4 | **Book Event** | Exact chain shown — see §5 | ✅ node-for-node |
| 5 | **Lookup Appointment** | Find an appointment by client/appointment details | Tool exists ✅; internals 🛠 |
| 6 | **Delete Appointment** | Delete the Calendar event for a found appointment | Tool exists ✅; internals 🛠 |
| 7 | **Update Appointment** | Update the Calendar event to a new time | Tool exists ✅; internals 🛠 |

## 5. Book Event — Exact Verified Chain ✅

Build this sub-workflow exactly as shown on screen:

1. **When Executed by Another Workflow** — receives the booking parameters from the agent (client, service, start, end).
2. **Create an event** (Google Calendar node) — create the event, e.g. title pattern `"<Service> – <Client Name>"` (demo result: *"Full Detail – Sam Carter"*), with the client email as attendee.
3. **Append row in sheet** (Google Sheets node → `Appointment Log`) — write the **Calendar event ID** into `ID`, plus Email, Appointment Type, Date (ISO with timezone, e.g. `2026-10-22T10:00:00+05:00`), and Notes.
4. **Booking Confirmation** — return the booking result to the agent (node type **Not confirmed from video**; a Set node is the natural choice 🛠).

## 6. Build the Main Workflow ✅

Create a workflow named **`ElevenLabs Receptionist (AI Agent)`** ✅ and add:

1. **ElevenLabs Webhook** (Webhook node) — receives the calls from ElevenLabs.
   - Payload the workflow expects ✅: `{ message, conversation_id, caller_phone }`.
   - Configure the node so the workflow's response is returned to the caller (pair with the Respond to Webhook node in step 4) 🛠 exact setting.
2. **Normalize Request** — map/rename the incoming fields into the agent's expected input 🛠 exact mapping (**Not confirmed from video**).
3. **AI Agent** node, agent type **Tools Agent** ✅, labelled "Appointment AI Agent" ✅. Attach:
   - **DeepSeek Chat Model** node ✅ with your DeepSeek credential.
   - **Call Memory** node ✅, session key = `conversation_id` ✅ (memory store type **Not confirmed from video** 🛠).
   - Seven **Execute Workflow tool** nodes ✅, one per sub-workflow from §4, named `Call 'Client Lookup'`, `Call 'New Client CRM'`, `Call 'Check Availability'`, `Call 'Book Event'`, `Call 'Lookup Appointment'`, `Call 'Delete Appointment'`, `Call 'Update Appointment'` ✅.
   - Write a system prompt that enforces the demonstrated behaviour 🛠 (the original prompt is **Not confirmed from video**): identify the client first → register if new → collect service/date/time → **always Check Availability before offering** → confirm with the caller → only then Book Event.
4. **Respond to ElevenLabs** (Respond to Webhook node) ✅ — respond with the JSON body `{ "response": "<agent reply>" }` ✅ (annotation verified on screen).
5. **Publish** the workflow ✅ (the demo workflow shows the Published state).

## 7. Connect ElevenLabs ✅ + 🛠

- In ElevenLabs, configure the conversational agent so each caller message is sent to the n8n webhook URL, and the returned `{ response }` is spoken ✅ (this contract is what the video demonstrates).
- Embed the agent's widget on the business website ✅. The demo widget shows: agent name "Hercules Receptionist", tagline "Books · reschedules · cancels", a "Start talking" button, a typed-input fallback, and quick-phrase chips ("Book a full detail", "What's free tomorrow?", "Reschedule", "Cancel") ✅.
- Exact ElevenLabs-side settings (voice choice, LLM settings on the ElevenLabs side, widget styling) are **Not confirmed from video** 🛠.

## 8. Test the Way the Video Does ✅

Reproduce the demo scenario end-to-end before going live:

1. Start a conversation as a **new** caller; ask for a Full Detail on a weekday at a set time, giving a test email.
   - Expect: Client Lookup runs → agent asks for full name + phone.
2. Provide name + test phone number.
   - Expect: New Client CRM adds the row; Check Availability runs for the requested window; agent offers the slot with its end time.
3. Confirm.
   - Expect: Book Event runs → Calendar event exists with you as attendee → Appointment Log row carries the **same event ID** → agent confirms and states an invite was sent.
4. Check the **Call Log** tab for the call summary with outcome "Booked".
5. Compare your n8n execution history with the demo's pattern: three successful executions (#49 / #51 / #54) with durations in the ~4–7s range.

## 9. Timezone Note ✅

The demo runs in **Pakistan Time (+05:00)**: availability windows and stored dates use `+05:00`, and the calendar event displays in PKT. Set your n8n instance, workflows, and calendar to your business's own timezone consistently — mixed timezones are the most common cause of wrong-slot bookings in systems like this 🛠.

---

*Evidence policy: see [PROJECT-LIMITATIONS.md](PROJECT-LIMITATIONS.md). Node-by-node behaviour: [WORKFLOW-EXPLANATION.md](WORKFLOW-EXPLANATION.md).*

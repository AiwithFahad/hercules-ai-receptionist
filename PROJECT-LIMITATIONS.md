# Project Limitations & Evidence Policy

This repository documents the **Hercules AI Receptionist** as a portfolio project. To keep it honest and credible, this page states exactly what the documentation is based on and where its limits are.

---

## 1. Source of Truth

- ✅ This documentation is based **solely on the project's demonstration video** (approx. 90 seconds; contents and timestamps listed in [demo/VIDEO-LINK.md](demo/VIDEO-LINK.md)).
- ✅ Every feature, node, integration, and result described in this repository was **visible or stated on screen** in that video, or is explicitly marked as inference/reconstruction.

## 2. What Is NOT Available

| Item | Status |
|---|---|
| Original n8n workflow JSON export | ❌ **Not available.** No workflow JSON is included in this repository, and none should be inferred from the documentation. The workflows would need to be rebuilt following [SETUP-GUIDE.md](SETUP-GUIDE.md). |
| Live deployment link | ❌ **Not available.** No public URL for the n8n instance, the ElevenLabs agent, or the Hercules Detailing website is provided. Nothing in this repository should be read as a link to a running system. |
| Agent system prompt | ❌ **Not shown in the video.** The agent's observable behaviour is documented; its prompt text is **Not confirmed from video**. |
| Source code | ❌ The system is a no-code/low-code n8n build; there is no application source code to publish. |
| API documentation | ❌ Beyond the webhook contract visible on screen (`{ message, conversation_id, caller_phone }` in, `{ response }` out), no formal API documentation exists for this project. |

## 3. What Cannot Be Verified From the Video

The following are marked **"Not confirmed from video"** wherever they arise, because the video does not show them:

- Exact node parameters (field mappings in Normalize Request, memory store type, model temperature/settings, credential and authentication configuration).
- Internal node layouts of six of the seven tool sub-workflows (only **Book Event** is opened on screen).
- Which component writes the **Call Log** entries.
- Whether Lookup/Delete/Update Appointment read or modify the Sheets Appointment Log in addition to Google Calendar.
- Error handling, retries, fallbacks, and double-booking protection — no failure path is shown.
- How the n8n instance itself is hosted, and the n8n/ElevenLabs/DeepSeek versions and pricing tiers in use.
- Production usage beyond the single demonstrated conversation — the executions shown are three runs from **20 Oct 2026 (PKT)** for one demo booking.

## 4. Demonstrated vs Supported

- **Demonstrated end-to-end (1 journey):** a new client books a Full Detail by voice — lookup, registration, availability check, booking, calendar event, CRM logging, call log.
- **Supported but NOT demonstrated:** rescheduling and cancelling. The `Update Appointment` and `Delete Appointment` tools exist on the canvas and the widget advertises both actions, but the video never shows either being used. Claims in this repository are worded accordingly.

## 5. About the Video Itself

- The video states on screen: *"Voices re-created for this video. Every receptionist reply shown is the live AI Agent's real response (n8n executions #49, #51, #54)."* In other words, the **audio voices are re-enactments**, while the **receptionist's replies are genuine outputs** from the real workflow runs referenced.
- The client in the demo is a **test client** using a demo email address and a fictional 555-range phone number. This documentation masks the phone number and refers to the email as a demo address; no real personal data is published here.
- Screenshots in `assets/` are **frames extracted from the video**, not captures from a live system.

## 6. Accuracy Commitment

If any statement in this repository goes beyond what the video supports, that is a documentation error. The correct reading is always the narrower one: if it isn't shown, it isn't claimed.

---

*Companion documents: [README.md](README.md) · [WORKFLOW-EXPLANATION.md](WORKFLOW-EXPLANATION.md) · [PROJECT-ARCHITECTURE.md](PROJECT-ARCHITECTURE.md) · [SETUP-GUIDE.md](SETUP-GUIDE.md)*

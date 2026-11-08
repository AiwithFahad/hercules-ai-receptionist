# GitHub Presentation Guide — hercules-ai-receptionist

How to publish and present this repository professionally.

---

## Repository Description (one line — paste into GitHub's "About" field)

> AI voice receptionist for a car detailing business — ElevenLabs voice agent + n8n AI Agent (DeepSeek) that books, reschedules and cancels appointments in Google Calendar and logs everything to a Google Sheets CRM.

**Website field:** leave empty — there is no live deployment link for this project (see PROJECT-LIMITATIONS.md). Add the demo video URL to the README instead once it's uploaded.

## Topics / Tags

```
n8n
ai-agent
elevenlabs
voice-ai
ai-receptionist
deepseek
llm
workflow-automation
google-calendar
google-sheets
appointment-booking
chatbot
automation
portfolio
```

## Suggested Commit Messages

Initial upload (one commit per logical unit reads professionally):

1. `docs: add README with project overview, architecture and demo evidence`
2. `docs: add node-by-node workflow explanation`
3. `docs: add project architecture with data flow and Mermaid diagram`
4. `docs: add setup guide (verified vs reconstructed steps marked)`
5. `docs: add project limitations and evidence policy`
6. `docs: add demo video index with scene timestamps`
7. `assets: add screenshots extracted from demo video (workflow, executions, title card)`

If uploading in a single commit instead:

```
docs: initial portfolio documentation for Hercules AI Receptionist

Complete documentation pack reconstructed from the project demo video:
README, workflow explanation, architecture, setup guide, limitations,
demo index and video-frame screenshots. No workflow JSON or live
deployment is claimed — see PROJECT-LIMITATIONS.md.
```

## Upload Instructions

### Option A — GitHub web UI (no tools needed)

1. Go to https://github.com/new — owner **AiwithFahad**, repository name `hercules-ai-receptionist`, Public, **do not** initialise with a README (this pack already contains one).
2. On the new repo page choose **"uploading an existing file"**, then drag in the contents of this folder (`README.md`, the four other `.md` files, `assets/`, `demo/`) — keep the folder structure intact.
3. Commit with the single-commit message above.
4. Click the ⚙️ next to **About**: paste the one-line description and add the topics listed above.
5. Optional but recommended: pin the repository on the profile, and replace the placeholder in `demo/VIDEO-LINK.md` with the real demo video URL once the video is uploaded to YouTube/Loom.

### Option B — Git command line

```bash
cd hercules-ai-receptionist
git init
git add .
git commit -m "docs: initial portfolio documentation for Hercules AI Receptionist"
git branch -M main
git remote add origin https://github.com/AiwithFahad/hercules-ai-receptionist.git
git push -u origin main
```

(Requires the empty repository to exist on GitHub first, as in Option A step 1.)

## Presentation Advice

- **Lead with the demo.** Recruiters and clients watch before they read: put the video link at the top of the README (the placeholder in the Demo section) and pin this repo next to your other AI-agent work.
- **Keep the honesty visible.** The limitations page is a strength, not a weakness — it shows engineering judgement. Don't remove the "Not confirmed from video" markers; a technical reviewer will notice and trust the rest more.
- **Never add what you can't prove.** If you later export the real workflow JSON or deploy a live demo, add them as a separate update commit (`feat: add workflow export` / `docs: add live demo link`) rather than rewriting history.
- **Use the execution evidence.** The strongest proof in this repo is the real execution panel (#49/#51/#54 with timings) — reference it in the repo description, your LinkedIn post, and client conversations.
- **One project, one story.** When sharing, pitch the business outcome first ("a detailing studio that never misses a booking, 24/7"), then the stack (ElevenLabs + n8n AI Agent + DeepSeek + Google).
- **Screenshots:** the three frames in `assets/` are genuine video frames. If you retake screenshots from the live system later, swap them in with a commit noting the change.

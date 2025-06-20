# Aspra – Voice-Based AI Scheduling Assistant

**Aspra** is a conversational voice agent built to schedule meetings in a natural, human-like way through real-time conversation. Using Vapi.ai, GPT-4o, n8n, and Google Calendar API, it handles multi-turn interactions, understands vague inputs, checks for availability, and confirms bookings — all over voice.

This project was submitted as part of the **NextDimension Take-Home Assignment** focused on intelligent voice agent design, LLM reasoning, and external tool integration.
![image](https://github.com/user-attachments/assets/4c63b549-653c-4468-bb53-bb7ffffae66b)


---

## 🔗 Live Demo Links

-  [Try Aspra on Vapi (Live Assistant)](https://vapi.ai?demo=true&shareKey=f357d247-1557-49fe-993c-37a3136cc5f0&assistantId=3620fd2c-6e97-4ceb-8c3b-c3798b8e10e8)
-  note the voice agent will be unable to confirm an appointment in the demo because of exhausted credits 
- [Demo Video ](https://youtu.be/DKkvHGbAwro)
- [Download n8n Workflow (.json)](https://anasisbatman.app.n8n.cloud/workflow/lvbxhR7AzmMBuMrx)
- ![image](https://github.com/user-attachments/assets/daaceb81-9996-4a66-8885-eb568b19ca29)


---

##  Tech Stack

| Component | Tool |
|----------|------|
| Voice Interface | [Vapi.ai](https://vapi.ai) |
| LLM | GPT-4o (via Vapi) |
| Orchestration | [n8n](https://n8n.io) |
| Calendar API | Google Calendar API |
| Internal Agent | AI Agent Node in n8n (uses Gemini / GPT-like logic) |
| Integration | Webhook between Vapi and n8n |

---

## Features

- Multi-turn conversation over voice
- Understands vague & relative inputs like “after lunch”, “next Tuesday”
- Calls tools only after all required info is confirmed
- Live availability checking via `getAvailability`
- Slot confirmation and booking via `BookAppointment`
- Voice UX optimized for tone, pauses, and human phrasing
- Works via both browser and phone call

---

##  Key Decisions & Tradeoffs

###  **Latency vs Natural Tone**
- Used short, casual phrases ("Alright... lemme check") to improve naturalness.
- Chose to **sacrifice a few hundred ms of latency** for richer voice response using ellipses (`...`) and filler words.
- Avoided robotic phrasing with smart chunking like:  
  _“Okay... I found two slots. 2 PM and 4:30 PM.”_

### **Workflow Design: Split vs Aggregate**
- Split logic in n8n into:
  - Webhook collection
  - Calendar API free/busy check
  - Formatting response with `Set` node
- Aggregated slot options and returned them to Vapi in structured JSON (for TTS to read out clearly).

###  **Using Gemini for Reasoning Inside n8n**
- In one version of the workflow, used the AI Agent node (Gemini-style) inside n8n to:
  - Parse vague time inputs
  - Validate intent
  - Smooth out edge-case corrections (e.g. “actually, make it 45 minutes instead”)

---

##  Bugs & Challenges

###  Tool Calls Not Triggering (Early)
- Initially, `getAvailability` was triggered **before all inputs were collected**.
- Solved by updating the system prompt to delay tool call until all three (duration, day, time) were explicitly confirmed.

###  Webhook Misfires
- Faced **multiple executions** from webhook due to auto-responses being interpreted as final answers.
- Resolved using state-based checks inside the n8n flow.

### 🎙 Flat Voice Output
- The voice (Sarah) felt robotic.
- Since I couldn’t access ElevenLabs' "Rachel", I added ellipses and natural filler in TTS responses to simulate **human pauses**.

---

## Sample Conversation

Aspra: Hey! I’m Aspra. How can I help you today?
User: I wanna book a meeting next Monday
Aspra: Cool. How long should it be?
User: Just half an hour
Aspra: Got it. Morning, afternoon, or later?
User: Afternoon would be perfect
Aspra: Alright... lemme check what’s open.

(getAvailability tool call runs via webhook)

Aspra: Okay, 2:00 PM and 4:30 PM are free. Which one sounds better?
User: Let’s go with 2
Aspra: You’re all set for 2 PM next Monday!
## Final Thoughts

Building *Aspra* helped me deeply understand how to blend:
- LLM-based reasoning with structured workflow tools (n8n)
- Tool-calling flow control
- UX challenges in real-time voice interaction
- Latency vs natural voice tradeoffs

---

##  License

Licensed under the [MIT License](./LICENSE).

---

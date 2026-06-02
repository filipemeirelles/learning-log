# WhatsApp AI Chatbot — Gym / Supplements Store

**Client type:** Small gym, personal training studio, or supplement shop  
**Goal:** Automate customer service, class scheduling, product inquiries, and lead capture 24/7  
**Platform:** n8n + OpenAI API + WhatsApp  
**Status:** Weekend build (06-07/06/2026)

---

## What This Bot Does

- Greets customers automatically
- Answers FAQs (hours, pricing, locations, products)
- Captures leads (name, phone, interest)
- Routes complex questions to human staff
- Sends promotional messages (opt-in)
- Logs all conversations to Google Sheets

---

## Architecture

```
Customer WhatsApp Message
        |
        v
  n8n Workflow Trigger
        |
        v
  OpenAI Agent (classifies intent)
        |
        +-- FAQ --> Pre-built response
        +-- Lead --> Save to Google Sheets
        +-- Appointment --> Send to staff
        +-- Unknown --> Human handoff
```

---

## Stack

- n8n (local or cloud)
- WhatsApp API (Twilio or Meta Business)
- OpenAI API (GPT-4o-mini for cost)
- Google Sheets (lead storage)

---

## Project Phases

See `build-guide.md` for step-by-step instructions.

# Build Guide — WhatsApp AI Chatbot for Gym/Supplements

**Estimated time:** 4-6 hours  
**Prerequisites:** n8n installed, OpenAI API key, Google account

---

## PRE-WORK (30 min)

### 1. Choose WhatsApp Provider

For small businesses, **Twilio WhatsApp API** is the fastest path:
- Sign up at twilio.com (free trial, ~$0.005/message)
- Get a WhatsApp sandbox number
- No business verification needed for testing

Alternative: **Meta WhatsApp Business Platform** (free, but requires business verification — slower).

**Recommendation:** Start with Twilio sandbox, migrate to Meta later when you have real clients.

### 2. Get Credentials Ready

You will need:
- `TWILIO_ACCOUNT_SID`
- `TWILIO_AUTH_TOKEN`
- `TWILIO_WHATSAPP_NUMBER` (e.g., `+141****8886`)
- `OPENAI_API_KEY`
- Google Sheets ID (for leads)

Store these in n8n Credentials (do NOT hardcode).

---

## PHASE 1: Basic Bot (1-2 hours)

### Step 1: Create Your Workflow

1. Open n8n: `http://localhost:5678`
2. Click **"Add first workflow"**
3. Name it: `WhatsApp Gym Bot`

### Step 2: Add WhatsApp Trigger

1. Click **+** to add a node
2. Search for **"WhatsApp"** (Twilio)
3. Select **"WhatsApp Trigger"**
4. Configure:
   - Credentials: Add your Twilio credentials
   - Webhook: n8n will give you a URL (e.g., `https://your-n8n.com/webhook/whatsapp`)
5. In Twilio Console → WhatsApp → Sandbox:
   - Paste the n8n webhook URL in "When a message comes in"
   - Save

**Test:** Send a WhatsApp message to your sandbox number. It should appear in n8n.

### Step 3: Add OpenAI Agent Node

1. Click **+** after the trigger
2. Search **"OpenAI"** → **"AI Agent"**
3. Configure:
   - Model: `gpt-4o-mini` (cheapest, fast)
   - System Prompt:
     ```
     You are a helpful assistant for [Gym Name/Supplements Store].
     
     Your knowledge:
     - Hours: Mon-Fri 6am-10pm, Sat 8am-2pm
     - Location: [address]
     - Personal training: R$ 80/session, packages available
     - Supplements: whey, creatine, pre-workout in stock
     - Classes: CrossFit, Yoga, Bodybuilding
     
     Rules:
     - Be friendly and concise
     - If someone asks about prices, give general ranges
     - If someone wants to sign up, collect: name, phone, goal
     - If you don't know, say you'll connect them to a human
     - Never make up information
     ```

### Step 4: Add Google Sheets Node (Lead Capture)

1. Click **+** after OpenAI
2. Search **"Google Sheets"** → **"Append Row"**
3. Configure:
   - Spreadsheet: Create one named `Gym Leads`
   - Sheet: `leads`
   - Columns: `timestamp`, `name`, `phone`, `interest`, `message`, `status`

### Step 5: Add Switch Node (Intent Routing)

Add a **Switch** node BEFORE OpenAI to classify intent:
- If message contains "price", "quanto custa", "valor" → Price info agent
- If message contains "sign up", "matricula", "aula" → Lead capture flow
- If message contains "human", "atendente", "falar com" → Human handoff
- Default → AI Agent

---

## PHASE 2: AI Agent with Memory (1-2 hours)

### Step 1: Add Memory to OpenAI Agent

In the AI Agent node:
1. Click **"Add memory"**
2. Select **"Window Buffer Memory"**
3. Token limit: 2000 (keeps last few messages in context)

### Step 2: Add Tools

Give the agent tools it can use:
- **Google Sheets Tool** — query leads
- **HTTP Request Tool** — check class schedule from API
- **Code Tool** — calculate pricing

---

## PHASE 3: Polish & Test (1 hour)

### Testing Checklist

- [ ] Bot responds to "Oi", "Hello", "Olá"
- [ ] Bot answers hours correctly
- [ ] Bot captures lead when asked about prices
- [ ] Bot says "I'll connect you to a human" for unknown questions
- [ ] Google Sheet gets new rows for leads
- [ ] Bot works after 30+ messages (memory test)

### Add Error Handling

Add an **Error Trigger** node at the end:
- If workflow fails, send message: "Sorry, I'm having technical issues. A human will contact you soon."

---

## PHASE 4: Go Live (when ready)

1. Move from Twilio sandbox to production number
2. Add the bot's phone number to gym website: "Chat with us on WhatsApp"
3. Set up auto-reply for off-hours
4. Create a simple admin dashboard (Google Sheets view)
5. Add weekly summary email to gym owner

---

## Cost Estimate (Small Gym)

| Item | Monthly Cost |
|------|-------------|
| Twilio WhatsApp (500 msgs) | ~$2.50 |
| OpenAI GPT-4o-mini (1K msgs) | ~$0.30 |
| n8n (self-hosted) | $0 |
| Google Sheets | $0 |
| **Total** | **~$3/month** |

---

## Next Steps After This

- Add voice notes support (audio transcription)
- Integrate Instagram DM
- Build admin panel (Streamlit)
- Add appointment booking calendar
- Multi-language support (PT + EN)

# AI-Competitor-Research-Creative-Intelligence-Engine
🕵️ AI-Powered Competitor Research &amp; Ad Creative Intelligence System Built with n8n • Meta Ads API • LLMs • Airtable • Web Scraping  An autonomous competitor research engine that scrapes Meta ads + landing pages, runs AI-based market analysis, and generates high-converting ad angles &amp; scripts — with built-in feedback loops.

This n8n workflow automates **Meta (Facebook) competitor ad research** and turns it into **AI‑generated ad angles and scripts** stored in Airtable. It helps performance marketers and agencies ship better creatives faster, without drowning in manual research.

---

## 🔥 What problem does this solve?

In real life, a media buyer or agency owner spends hours on:

- Manually checking Facebook Ad Library for competitor ads.  
- Opening landing pages, reading copy, taking screenshots, and jotting notes.  
- Trying to understand what angles competitors are using.  
- Turning that research into new hooks, angles, and ad scripts for their own clients.

This process is:

- Time‑consuming and repetitive.  
- Easy to skip when you’re busy (so creative quality drops).  
- Hard to systemize across a team.

This workflow turns all of that into an **automated engine**:

> “Give me my product + some competitors, and I’ll keep generating fresh, research‑driven ad angles & scripts, organized neatly in Airtable.”

---

## 🧠 High‑level overview

The workflow does four main things:

1. **Pull competitor ad & landing page data**  
2. **Combine it with your product info**  
3. **Use AI to generate angles, hooks, and scripts**  
4. **Store everything in Airtable with a feedback loop**

Tech stack:

- **n8n** – orchestration  
- **Meta Marketing API** – competitor ads (accounts, campaigns, creatives)  
- **Scrapfly / HTTP Request** – competitor landing page scraping  
- **Perplexity / LLM via HTTP** – product & customer research summary  
- **OpenAI / Gemini / OpenRouter LLM** – angles & scripts generation  
- **Airtable** – storing products, angles, scripts & feedback

---

## 🧩 Detailed workflow steps

### 1. Triggers & product selection

- The workflow is triggered via:
  - **Schedule** (e.g. daily/weekly competitor refresh), or  
  - **Webhook** (to research on demand for a new product).  
- Nodes like `Get Product`, `Get Product1` fetch product details from Airtable:
  - Product name, description, target audience, offer details, USPs. [page:46]  

This means you can maintain a catalog of products/clients in Airtable and run the engine per product.

---

### 2. Fetch competitor ads & creatives

- Nodes like `Get FB Ad Accounts`, `Get Ad Campaigns`, `Get FB Ad Sets`, `Get FB Ad Contents` call the **Meta Marketing API** to pull: [page:46][web:53]  
  - Active campaigns & ad sets  
  - Creatives (images, videos, primary text, headlines, links)  
  - Performance stats where relevant  

This gives you a structured view of what your competitors are running right now.

---

### 3. Scrape competitor landing pages

- `HTTP Request` + `Scrapfly` node take competitor ad URLs and scrape landing page HTML. [page:46][web:53]  
- `Code` node processes the HTML:
  - Extracts readable text (headlines, bullets, guarantees, social proof, FAQs)  
  - Removes scripts, boilerplate, and junk markup  

Now instead of raw HTML, your AI gets **clean, readable competitor copy**.

---

### 4. Product & customer research with AI

- A **Perplexity / LLM** node is called with:
  - Your product details from Airtable  
  - Extracted competitor landing page copy  
- It returns a structured **research summary**:
  - Typical customer pain points  
  - Desired outcomes and objections  
  - How your product is different / better  
  - What angles competitors are currently using [page:46][web:57]  

This automates what a good strategist would normally do during deep research.

---

### 5. Generate ad angles

- A dedicated LLM chain (e.g. "Generate Angles") uses:
  - Product info  
  - Research summary  
  - Competitor angles  
- It outputs a list of **clear, named angles**, such as:
  - “Time‑Saving Automation Angle”  
  - “Social Proof / Case Study Angle”  
  - “Risk‑Free Trial Angle”  
  - “High ROI vs. Agency Fees Angle” [page:46][web:56]  

Output is forced into JSON (via structured parser), so each angle is a clean record.

---

### 6. Generate full ad scripts

For each angle, another LLM chain (e.g. "Generate Script") produces full ads:

- Hook line(s)  
- Primary text  
- Body copy and CTA  
- Optional video storyboard (scene‑by‑scene) [page:46]  

Again, output is structured JSON, so you get one row per script with all fields.

---

### 7. Store everything in Airtable

Using `Split Out`, `Aggregate`, and Airtable nodes:

- New angles are stored in an **Angles** table.  
- New scripts are stored in a **Scripts** table and linked to:
  - Product  
  - Angle  
  - Version (V1, V2, etc.) [page:46]  

You end up with a searchable library:  
**Product → Angle → Scripts → Status → Notes**

---

### 8. Feedback & iteration loop

The system isn’t just “generate once, forget”.

- In Airtable, you can set fields like:
  - `status` = approved / needs_changes / rejected  
  - `feedback` = “too generic”, “make it shorter”, “focus more on ROI”  
- The workflow periodically checks for scripts/angles that need changes via `If` nodes (If6, If7, If9, etc.). [page:46]  
- If an item needs revision:
  - The AI is called again with the original script + your feedback.  
  - A new improved version is generated and saved as the next version.  

This creates a **closed feedback loop** for continuous creative improvement.

---

### 9. Optional: voice‑note input

- A node like `OpenAI Transcribe Recording` can take a founder’s or media buyer’s voice note: [page:46]  
  - “I want a new ad focused on how we save agencies 10+ hours per week.”  
- It converts to text and sends into the same angle/script pipeline.

---

## 🧪 Real‑life scenario

Imagine you run a performance agency for coaching businesses:

- You plug in your client’s product details into Airtable.  
- Add 3‑5 key competitor ad URLs or accounts.  
- The workflow:
  - Pulls competitor ads + landing pages  
  - Understands your product & customers  
  - Generates 10–20 angles and dozens of ad scripts  
  - Saves them neatly into Airtable for review  
- You just open Airtable, filter by “approved = false”, review, mark the best ones, and send them to your creative team or directly into Meta.

Time saved: **hours of manual research + writing per client per week**.  
Quality gained: **creatives grounded in real market & competitor data**.

---

## ⚙️ Setup & requirements

- n8n (self‑hosted or cloud)  
- Meta Marketing API credentials (app + access tokens)  
- Scrapfly (or any scraping API) key  
- Airtable base with:
  - Products table  
  - Angles table  
  - Scripts table  
- LLM provider:
  - Perplexity / OpenRouter / OpenAI / Gemini for research + generation  

> You can swap the LLM provider by just updating the HTTP Request / LLM nodes – the workflow logic stays the same.

---

## 📂 Files

- `/workflow.json` – export of the n8n workflow  
- `/docs/` – screenshots, Airtable schema, example outputs  
- `/prompts/` – prompts used for research, angles, and scripts

---

## 🚀 Future improvements

- Push approved scripts directly to a “Creative Brief” doc / Notion.  
- Auto‑generate thumbnails or storyboard screenshots with image models.  
- Track which angles/scripts lead to best ROAS and feed that back as training signal for the LLM prompts.

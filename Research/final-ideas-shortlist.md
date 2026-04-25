# Final Ideas Shortlist — JobEscape Product Expansion

*New standalone tools / platforms to build alongside or inside the JobEscape ecosystem. Each idea follows the same model: new course funnel → tool as upsell/companion.*

---

## 1. ScriptKit — YouTube Video Pack Generator

**For:** Faceless YouTube creators learning through the "Faceless YouTube Automation" course

**The problem:** Editing is handled by CapCut. The real time sinks are: finding a viral-worthy topic/title, writing a script with the right structure, and generating a voiceover. Together these take 2–4 hours per video.

**What it does:**
- User inputs a video topic
- Output package:
  - 5 title options (CTR-optimized)
  - Full script (proven viral structure: hook → body → CTA)
  - Audio voiceover file (ElevenLabs integration — ready to drop into CapCut)
  - 5 thumbnail concepts (what to write, what to show)
  - YouTube SEO description + tags

**Why it's simple to build:**
- Claude API → script + titles + thumbnail ideas + SEO
- ElevenLabs API → voiceover
- Simple web UI, no video rendering, no publishing pipeline

**Monetization:** Base generations included in JobEscape subscription. More voices and more generations → credit top-ups (same model as Genescape).

**Positioning:** "From topic idea to production-ready package in 2 minutes."

---

## 2. DropKit — Product Photo & Ad Kit

**For:** People taking the "AI for E-commerce" course

**Core logic:** Sell them the course → during the course, at the lesson about product packaging, they create their first product photos and ad copy through JobEscape directly → they don't go to PhotoRoom + Flair.ai + Canva → they pay us for credits instead.

**What it does — 4 steps, nothing more:**
1. **Upload a product photo** — raw shot on any background, even a phone photo
2. **Describe the product briefly** — name, 1 sentence about what it does
3. **Pick a vibe** — lifestyle scene options (living room, outdoor, flat lay, branded studio)
4. **Generate the kit:**
   - 5 lifestyle photos
   - 3 ad copy captions ready to post
   - 1 short review video — AI avatar delivers a 20–30s product review from a pre-built template (hook → benefit → CTA). User picks avatar, script is auto-generated from the product description.

**What it does NOT have:**
- No custom avatar creation
- No bulk processing
- No URL import
- No POD mockups
- No white-label or agency features

**Why it's simple to build:**
Genescape already does image generation. This is a **product photography mode inside Genescape** — a focused UI on top of existing infrastructure, not a new product.

**The wow moment:** User is on the "package your product" lesson → hits "Create product photos now" → uploads a plain photo of their product → gets 5 professional lifestyle shots and 3 ready-to-use ad captions in 2 minutes → "I can't believe this actually works."

**Monetization:** Credits per kit (same as Genescape model). A few free credits included in the course, more on top-up.

---

## 3. AgentStore — AI Agent Template Marketplace

**For:** AI automation freelancers learning through the "AI Agents Mastery" course (extension of existing AI Automation Specialist track)

**The problem:** Freelancers learn to build n8n/Make agents but start from scratch every time. Clients want to see "what can you build for me?" — there's no portfolio or starting point library.

**What it does:**
- Library of pre-built n8n / Make.com agent templates organized by use case:
  - Lead generation agent
  - Customer service chatbot
  - Research & reporting agent
  - Social media outreach agent
  - Email follow-up sequence agent
  - etc.
- One-click deploy / customize
- Graduates can submit their own agents to sell in the store

**The flywheel:**
JobEscape teaches → user builds agent → sells it in AgentStore → store grows with user-generated templates → attracts new users who want ready-made agents

**Monetization:**
- Templates included in subscription (basic library)
- Premium templates sold individually ($5–30 each)
- Revenue share when users sell their own templates (JobEscape takes 20–30%)

---

## 4. UGC Creator — AI Avatar Video Mode

**For:** People taking the "AI UGC Content Creator" course

**Core logic:** Sell them the course → during the course, they create UGC videos through JobEscape directly → they don't go to HeyGen or MakeUGC.ai → they pay us for credits instead.

Target: not professionals, not pure beginners. People mid-course who want a "wow, I actually made this" moment.

**What it does — 3 steps, nothing more:**
1. **Pick an avatar** — 10–15 pre-built AI personas (different faces, ages, styles). No custom avatar creation needed.
2. **Describe the product or paste a script** — AI generates a UGC-style script (hook → review → CTA). Or the user writes their own.
3. **Generate the video** — avatar delivers the script on camera. 30–60 seconds. Download and done.

**What it does NOT have:**
- No brand marketplace
- No brief management
- No usage rights tracking
- No client delivery workflow

**Why it's simple to build:**
Genescape already has avatar creation, video generation, and lip sync. This is a **UGC mode inside Genescape** — a simplified UI on top of existing infrastructure, not a new product.

**The wow moment:** User is on lesson 3 of the course → sees "Create your first UGC video" button → makes a realistic product review video in 5 minutes without leaving JobEscape → feels the subscription is already worth it.

**Monetization:** Credits per video generation (same as Genescape model). A few free credits included in the course, more on top-up.

---

## 5. Claude Skills Store — Curated Claude Role Library

**For:** All JobEscape users, especially those in the "Master the Claude" course

**The problem:** Claude is powerful but most users don't know how to unlock specific behaviors from it. The skill is in knowing *how to instruct* Claude — and that knowledge is scattered across GitHub repos, Reddit threads, and power user communities.

**What it is:**
A curated collection of Claude skill files — markdown documents that inject a specific role, persona, or behavior mode into Claude. Each skill transforms Claude into a specialist for one job.

Think of it like the `llm-council` skill: a markdown file that, when loaded, makes Claude run a structured 5-advisor council session. The file *is* the skill — it defines the behavior, the format, the rules. User activates it → Claude becomes that thing.

**How it works:**
- JobEscape collects, curates, and packages Claude skill `.md` files from public sources (GitHub collections, community repos) + writes originals
- User browses the library, picks a skill, activates it → Claude immediately takes on that role inside JobEscape
- Zero engineering overhead — no backend needed beyond the existing Claude API integration

**Example skills in the library:**
- `Brutal Business Idea Critic` — tears apart your idea like a skeptical investor
- `Cold Email Machine` — writes outreach sequences in your voice
- `Freelance Proposal Writer` — structures winning Upwork/Fiverr proposals
- `YouTube Hook Generator` — rewrites your opening 30 seconds for max retention
- `Competitor Teardown Analyst` — structured breakdown from a URL or description
- `LLM Council` *(already built)* — 5-advisor pressure test on any decision
- `Pricing Strategist` — sets freelance rates based on market + positioning
- `Client Objection Handler` — roleplay difficult client conversations
- `Reddit Pain Point Miner` — extracts problems worth solving from any subreddit
- `Launch Copywriter` — writes landing page copy from a brief

**Free vs. paid:**
- Free: 10 skills included in base subscription
- Full library (50–200+ skills): premium add-on or higher tier
- New skills added monthly → retention mechanic

**Why it's simple to build:**
Skills are markdown files. Sourcing = curating existing public collections + writing originals. This is a **content and curation play**, not an engineering play. No new infrastructure required.

**Competitive angle:**
Skill Leap has 5,500+ static prompt templates — you copy, paste, edit manually. Claude Skills Store is different: skills are *behavioral instruction sets*, not prompts. The user doesn't edit anything — they activate a skill and Claude becomes something specific. That's a fundamentally different product and much harder to replicate with a simple template library.

---

## Summary

| Idea | Course funnel | Core value | Complexity to build |
|---|---|---|---|
| **ScriptKit** | Faceless YouTube Automation | Script + voice + SEO in 2 min | Low (Claude API + ElevenLabs) |
| **DropKit** | AI for E-commerce | Lifestyle photos + ad copy from one product photo | Low-Medium (product photo mode on top of Genescape) |
| **AgentStore** | AI Agents Mastery | Deploy pre-built agents, sell your own | Medium-High (marketplace mechanics) |
| **UGC Creator** | AI UGC Content Creator | Pick avatar → script → video in 3 steps | Low-Medium (UGC mode on top of Genescape) |
| **Claude Skills Store** | Master the Claude (+ all tracks) | Activate pre-built Claude roles — no prompting, no editing | Low (markdown files + curation, existing Claude API infra) |

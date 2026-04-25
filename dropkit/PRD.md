# DropKit — Product Requirements Document

**Version:** 1.0  
**Date:** April 2026  
**Status:** Pre-build  
**Owner:** JobEscape Product Team

---

## 1. Overview

### What Is DropKit?

DropKit is an AI-powered product branding and content kit generator embedded inside JobEscape. In 4 steps and under 2 minutes, a user uploads a raw product photo and receives a complete content package: 5 professional lifestyle photos, 3 ad copy captions, and 1 short AI avatar review video — ready to post on TikTok, Instagram, or Shopify.

DropKit is not a standalone product. It lives inside JobEscape as a tool companion to the **AI for E-commerce course**, activated at the moment in the curriculum when the user learns about product presentation and branding.

### One-Line Pitch

"Upload your product photo. Get a full brand kit in 2 minutes."

### What It Is Not

- Not a professional photo studio replacement
- Not a bulk processing tool
- Not a white-label or agency platform
- Not a standalone app
- Not a POD mockup generator
- Not a video editing suite

---

## 2. Problem Statement

People taking the AI for E-commerce course hit a wall at the product content stage. To look professional, they need:

1. Lifestyle product photos → normally requires PhotoRoom, Flair.ai, or hiring a photographer ($500–$5,000/shoot)
2. Ad copy captions → requires knowing copywriting frameworks
3. A product review video → requires HeyGen, MakeUGC.ai, or filming themselves

Combined, these take 3–6 hours and require 3+ separate paid tools. Most beginners abandon their store before ever posting their first product.

DropKit eliminates all of this in one flow, inside the app they're already using.

---

## 3. Target User

**Primary:** JobEscape subscriber currently enrolled in the AI for E-commerce course.

**Profile:**
- Non-technical, 24–40 years old
- Lives in US, UK, CA, AU
- Wants to start a Shopify/Etsy/TikTok Shop store as a side income
- Has a product idea or existing product but no budget for professional content
- Mid-course — they've learned the concept, now they want to produce something real

**They are NOT:**
- Professional e-commerce operators with existing content teams
- Agency owners processing dozens of products
- Developers or designers

---

## 4. User Journey & Entry Points

### Primary Entry Point: In-Course Trigger

The main access point is a **contextual CTA embedded inside the course**, specifically in Module 4 (Product Photography & Branding) of the AI for E-commerce curriculum.

At the lesson titled **"Create Your First Product Photos with AI"**, the lesson UI shows:

> *"For this lesson, we're going to use DropKit — JobEscape's built-in product kit generator. You'll create your first professional lifestyle photos right now, without leaving the app."*

Below the lesson description: **[Open DropKit →]** button.

This is the "wow moment" — the user does the task from the lesson inside the same platform, immediately.

### Secondary Entry Points

| Entry point | Location | Context |
|---|---|---|
| Dashboard shortcut | JobEscape home dashboard | "Tools" section alongside Genescape, Job Hunter |
| Course completion screen | End of Module 4 | "Generate your full product kit now" |
| Upsell modal | After free credit depletion | Prompt to buy more credits |
| Direct navigation | `/tools/dropkit` | URL accessible from nav |

### User Flow

```
Course Lesson (Module 4) 
    ↓
[Open DropKit] button
    ↓
Step 1: Upload product photo (any background, any device)
    ↓
Step 2: Describe the product (name + 1 sentence)
    ↓
Step 3: Pick a vibe (lifestyle scene: living room / outdoor / flat lay / branded studio)
    ↓
Step 4: Generate
    ↓
Output delivered:
  ├── 5 lifestyle photos (download individually or as ZIP)
  ├── 3 ad captions (copy to clipboard)
  └── 1 review video — AI avatar delivers 20–30s product review
    ↓
Credit deducted
    ↓
[Back to course] or [Generate another kit]
```

---

## 5. Core Features

### 5.1 Photo Upload
- Accepts: JPG, PNG, WEBP
- Max file size: 10MB
- Any background accepted (will be replaced by AI)
- Mobile camera upload supported
- No cropping or editing required from user

### 5.2 Product Description Input
- Product name field (max 60 chars)
- One-sentence description field (max 200 chars)
- Optional: product category tag (dropdown) for better image relevance

### 5.3 Vibe / Scene Selection
- 4 preset lifestyle scenes:
  1. **Living Room** — cozy home setting, warm tones
  2. **Outdoor** — natural light, grass/stone/wood
  3. **Flat Lay** — clean top-down on neutral surface
  4. **Branded Studio** — white/dark background, product-centred
- Single selection only (keeps generation simple and fast)

### 5.4 Lifestyle Photo Generation
- Output: 5 images
- Resolution: 1024×1024 minimum (square, suitable for Instagram/Etsy)
- Product kept visually consistent across all 5 shots
- No watermarks on downloaded files
- Download as individual files or ZIP

### 5.5 Ad Copy Generation
- Output: 3 caption variants
- Each caption follows a proven format:
  - Hook line (pattern interrupt)
  - Benefit statement
  - CTA (Buy now / Link in bio / Shop below)
- Tones vary across 3 captions: emotional, practical, curiosity-driven
- Copy to clipboard in one tap

### 5.6 Review Video (Avatar)
- Output: 1 video, 20–30 seconds
- Format: 9:16 vertical (TikTok/Reels/Shorts ready)
- Flow: hook → product benefit → CTA
- Script auto-generated from product description
- AI avatar delivers script on camera (pre-built persona, not custom)
- 10–15 avatar options (different ages, genders, styles)
- No custom avatar creation (reduces complexity)
- Download as MP4

---

## 6. What DropKit Does NOT Have (Scope Guardrails)

| Feature | Status | Reason |
|---|---|---|
| Custom avatar creation | ❌ Out of scope | Complexity + covered by Genescape separately |
| Bulk product processing | ❌ Out of scope | Not the target user |
| URL import (product page scan) | ❌ Out of scope | Over-engineering v1 |
| White-label or agency mode | ❌ Out of scope | Wrong audience |
| POD mockups (t-shirts, mugs) | ❌ Out of scope | Different workflow |
| Publishing to Shopify/Etsy directly | ❌ Out of scope | Integration complexity |
| Video editing tools | ❌ Out of scope | CapCut handles this |
| Multiple scene selections per kit | ❌ Out of scope | Adds confusion, increases cost |

---

## 7. Credit & Monetization Model

### Credits System
DropKit uses the same credit system as Genescape. Each generation action consumes credits from the user's JobEscape account.

| Action | Credit cost |
|---|---|
| Full kit (5 photos + 3 captions + 1 video) | 50 credits |
| Photos only (5 images) | 25 credits |
| Video only | 30 credits |
| Captions only | 5 credits |

### Free Credits
- Users enrolled in the AI for E-commerce course receive **100 free DropKit credits** (2 full kits) when they reach Module 4
- These credits are course-specific and non-transferable to Genescape

### Credit Top-Up Pricing (same as Genescape model)

| Pack | Credits | Price |
|---|---|---|
| Starter | 200 | $4.99 |
| Standard | 500 | $9.99 |
| Pro | 1,200 | $19.99 |

### Subscription Integration
- Users on annual JobEscape plan receive 200 DropKit credits/month included
- Monthly subscribers: 50 credits/month included (1 kit)

---

## 8. JobEscape Integration Points

| System | Integration |
|---|---|
| Auth | JobEscape session token — no separate login |
| Credits | Shared wallet with Genescape |
| Course system | CTA injected at lesson level (Module 4, Lesson 3) |
| Dashboard | Listed under "AI Tools" alongside Genescape and Job Hunter |
| Notifications | Push notification when kit is ready (generation takes 30–90s) |
| Analytics | Generation events tracked in JobEscape user analytics |

---

## 9. Success Metrics

| Metric | Target (90 days post-launch) |
|---|---|
| % of Module 4 students who open DropKit | >40% |
| % of openers who complete a full kit | >60% |
| Average kits generated per active user/month | 3+ |
| Credit top-up conversion rate | >15% of free credit users |
| Course completion rate change (Module 4 cohort) | +10% vs. pre-DropKit baseline |
| NPS lift in post-course survey | +15 points |

---

## 10. v1 Scope Summary

**In v1:**
- Full 4-step kit generation flow
- 5 lifestyle photos
- 3 ad captions
- 1 review video (avatar, vertical format)
- 4 scene presets
- Credit deduction + top-up modal
- In-course CTA placement (Module 4)
- Dashboard access

**Deferred to v2:**
- Multiple scene variants per kit
- User avatar customization
- Direct Shopify/Etsy publish
- A/B caption testing
- Batch processing
- Community kit sharing ("show your kit")

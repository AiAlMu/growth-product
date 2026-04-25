# DropKit — Technical Implementation

**Stack:** Next.js 14+ · Vercel · Google AI (Gemini + Imagen + Veo) · Vercel Blob · Neon (PostgreSQL)  
**Target env:** Production on Vercel  
**Auth:** JobEscape JWT token passthrough  
**Date:** April 2026

---

## 1. Architecture Overview

```
User (JobEscape app)
        ↓
  JobEscape Frontend
  (injects DropKit iframe or routes to /tools/dropkit)
        ↓
  DropKit Next.js App (Vercel)
        ↓
  ┌─────────────────────────────────┐
  │         API Routes              │
  │  /api/generate-photos           │  → Imagen 3 (Vertex AI)
  │  /api/generate-captions         │  → Gemini 2.0 Flash
  │  /api/generate-video-script     │  → Gemini 2.0 Flash
  │  /api/generate-video            │  → Veo 2 (Vertex AI)
  │  /api/credits/check             │  → Neon DB
  │  /api/credits/deduct            │  → Neon DB
  └─────────────────────────────────┘
        ↓
  Vercel Blob Storage
  (generated images + videos stored here, returned as URLs)
```

---

## 2. Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend framework | Next.js 14 (App Router) | Vercel-native, streaming support, server actions |
| Hosting | Vercel | Zero-config deployment, built-in Blob storage, Edge functions |
| Image generation | Google Imagen 3 via Vertex AI | Best quality product photography among available models |
| Text generation | Gemini 2.0 Flash | Fast, cheap, good copywriting output |
| Fast/cheap text | Gemini 2.0 Flash-Lite (Nano) | Ad captions — high volume, low latency needed |
| Video generation | Veo 2 via Vertex AI | Only available model in the stack for video |
| File storage | Vercel Blob | Stores all generated images + video output |
| Database | Neon (PostgreSQL) | Credits tracking, generation history |
| Auth validation | JobEscape JWT | No separate auth system needed |
| Styling | Tailwind CSS | Fast UI development |

---

## 3. Google AI Model Allocation

| Task | Model | Why this model |
|---|---|---|
| 5 lifestyle product photos | **Imagen 3** (via Vertex AI) | Highest quality image generation; best at product-in-scene compositing |
| 3 ad copy captions | **Gemini 2.0 Flash-Lite** | Fast + cheap for short text output; runs in <1s |
| Video script (20–30s) | **Gemini 2.0 Flash** | Reliable narrative structure; good at persuasive short-form copy |
| Review video generation | **Veo 2** (via Vertex AI) | Text-to-video with human presenter capability |

### Imagen 3 — Product Photo Generation

```typescript
// Each of 5 photos gets a unique scene-specific prompt
const scenePrompts = {
  living_room: "cozy modern living room, warm ambient lighting, wooden surface",
  outdoor: "natural outdoor setting, soft daylight, stone or grass surface",
  flat_lay: "clean flat lay, neutral white or linen background, top-down view",
  branded_studio: "professional product studio, soft box lighting, pure white background"
}

const photoPrompt = `
  Professional lifestyle product photography.
  Product: ${productName}. ${productDescription}.
  Scene: ${scenePrompts[selectedVibe]}.
  The product is the clear focal point.
  Style: commercial photography, high-end e-commerce.
  Shot ${i + 1} of 5 — vary the angle and composition slightly from previous shots.
  Photorealistic, sharp focus on product, bokeh background.
`
```

API call (Vertex AI Node SDK):
```typescript
import { VertexAI } from "@google-cloud/vertexai"

const vertexAI = new VertexAI({ project: process.env.GCP_PROJECT_ID, location: "us-central1" })
const imageModel = vertexAI.getGenerativeModel({ model: "imagen-3.0-generate-001" })

const response = await imageModel.generateContent({
  contents: [{ role: "user", parts: [{ text: photoPrompt }] }],
  generationConfig: {
    // Imagen 3 specific params
    numberOfImages: 1,
    aspectRatio: "1:1",
    outputMimeType: "image/jpeg"
  }
})
```

Generate all 5 in parallel with `Promise.all()`.

### Gemini Flash-Lite — Ad Copy Generation

```typescript
import { GoogleGenerativeAI } from "@google/generative-ai"

const genAI = new GoogleGenerativeAI(process.env.GOOGLE_AI_API_KEY)
const model = genAI.getGenerativeModel({ model: "gemini-2.0-flash-lite" })

const captionPrompt = `
You are an e-commerce copywriter. Write 3 ad captions for this product.

Product: ${productName}
Description: ${productDescription}

Rules:
- Each caption max 150 characters
- Caption 1: Emotional angle (how it makes them feel)
- Caption 2: Practical/benefit angle (what it does)
- Caption 3: Curiosity/question hook
- Each ends with a CTA: "Shop now →", "Link in bio", or "Tap to shop"
- No hashtags
- Output as JSON array: ["caption1", "caption2", "caption3"]
`

const result = await model.generateContent(captionPrompt)
const captions = JSON.parse(result.response.text())
```

### Gemini Flash — Video Script Generation

```typescript
const scriptPrompt = `
Write a 20–30 second UGC-style product review script for a social media video.

Product: ${productName}
Description: ${productDescription}

Structure:
- Hook (0–3s): Surprising statement or relatable problem
- Product intro (3–8s): Natural introduction, not salesy
- Key benefit (8–20s): One specific benefit with a concrete example
- CTA (20–30s): Direct call to action

Tone: Conversational, authentic, first person ("I", "you")
Output: Plain script text only. No stage directions. No timestamps.
Max 80 words.
`
```

### Veo 2 — Review Video Generation

```typescript
// Veo 2 via Vertex AI — text-to-video
const videoModel = vertexAI.getGenerativeModel({ model: "veo-2.0-generate-001" })

const videoPrompt = `
A confident, friendly person (${selectedAvatar.description}) 
speaking directly to camera in a well-lit room.
They are reviewing a product called ${productName}.
Script: "${generatedScript}"
Style: UGC, authentic, vertical 9:16 format, phone-camera aesthetic.
Duration: 20–30 seconds.
No text overlays. No background music.
`

const videoResponse = await videoModel.generateContent({
  contents: [{ role: "user", parts: [{ text: videoPrompt }] }],
  generationConfig: {
    aspectRatio: "9:16",
    durationSeconds: 25
  }
})
```

> **Note on Veo 2 availability:** Veo 2 generation is async — it returns a job ID, not immediate output. Poll for completion using the operations API. Typical generation time: 60–120 seconds. Use Vercel's streaming response or a webhook pattern to notify the frontend when the video is ready.

---

## 4. File Storage — Vercel Blob

All generated assets are stored in Vercel Blob and returned as public URLs.

```typescript
import { put } from "@vercel/blob"

// Store a generated image
const { url } = await put(
  `dropkit/${userId}/${kitId}/photo_${index}.jpg`,
  imageBuffer,
  { access: "public", contentType: "image/jpeg" }
)

// Store generated video
const { url: videoUrl } = await put(
  `dropkit/${userId}/${kitId}/review_video.mp4`,
  videoBuffer,
  { access: "public", contentType: "video/mp4" }
)
```

File lifecycle: generated assets are kept for 7 days, then auto-deleted (set via Blob TTL). Users should download within that window.

---

## 5. Database Schema (Neon / PostgreSQL)

```sql
-- Credits wallet (shared with Genescape)
CREATE TABLE user_credits (
  user_id       VARCHAR(255) PRIMARY KEY,
  balance       INTEGER NOT NULL DEFAULT 0,
  updated_at    TIMESTAMP DEFAULT NOW()
);

-- Generation history
CREATE TABLE dropkit_generations (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         VARCHAR(255) NOT NULL,
  kit_id          VARCHAR(255) NOT NULL,
  product_name    TEXT,
  product_desc    TEXT,
  vibe            VARCHAR(50),
  avatar_id       VARCHAR(50),
  photo_urls      TEXT[],        -- array of 5 Vercel Blob URLs
  caption_1       TEXT,
  caption_2       TEXT,
  caption_3       TEXT,
  video_script    TEXT,
  video_url       TEXT,
  credits_used    INTEGER,
  status          VARCHAR(20) DEFAULT 'pending',  -- pending | generating | complete | failed
  created_at      TIMESTAMP DEFAULT NOW(),
  expires_at      TIMESTAMP DEFAULT NOW() + INTERVAL '7 days'
);

-- Course credit grants (free credits when reaching Module 4)
CREATE TABLE course_credit_grants (
  user_id         VARCHAR(255) NOT NULL,
  course_id       VARCHAR(255) NOT NULL,
  module_id       VARCHAR(255) NOT NULL,
  credits_granted INTEGER NOT NULL,
  granted_at      TIMESTAMP DEFAULT NOW(),
  PRIMARY KEY (user_id, course_id, module_id)
);
```

---

## 6. API Routes

### `POST /api/generate-kit`

Main generation endpoint. Validates credits, triggers all generation in parallel where possible, returns kit ID.

```typescript
// app/api/generate-kit/route.ts

export async function POST(req: Request) {
  const { productName, productDesc, vibe, avatarId } = await req.json()
  
  // 1. Validate JobEscape JWT
  const userId = await validateJobEscapeToken(req.headers.get("Authorization"))
  if (!userId) return Response.json({ error: "Unauthorized" }, { status: 401 })
  
  // 2. Check credit balance
  const balance = await getUserCredits(userId)
  if (balance < 50) return Response.json({ error: "Insufficient credits" }, { status: 402 })
  
  // 3. Create kit record
  const kitId = crypto.randomUUID()
  await createKitRecord(kitId, userId, { productName, productDesc, vibe, avatarId })
  
  // 4. Deduct credits optimistically
  await deductCredits(userId, 50)
  
  // 5. Kick off generation (returns immediately, generation is async)
  generateKitAsync(kitId, { productName, productDesc, vibe, avatarId })
  
  return Response.json({ kitId, status: "generating" })
}
```

### `GET /api/kit-status/[kitId]`

Poll endpoint — frontend polls every 3 seconds until status is `complete`.

```typescript
export async function GET(req: Request, { params }: { params: { kitId: string } }) {
  const kit = await getKitById(params.kitId)
  
  return Response.json({
    status: kit.status,           // pending | generating | complete | failed
    photos: kit.photo_urls,       // null until complete
    captions: [kit.caption_1, kit.caption_2, kit.caption_3],
    videoUrl: kit.video_url,      // null until video ready
    videoStatus: kit.video_status // separate status for video (slower)
  })
}
```

### `POST /api/credits/add`

Called by JobEscape backend when user purchases credits or reaches Module 4 milestone.

```typescript
export async function POST(req: Request) {
  // Validate internal JobEscape service key
  const serviceKey = req.headers.get("X-JobEscape-Service-Key")
  if (serviceKey !== process.env.JOBESCAPE_SERVICE_KEY) {
    return Response.json({ error: "Forbidden" }, { status: 403 })
  }
  
  const { userId, credits, reason } = await req.json()
  await addCredits(userId, credits)
  
  return Response.json({ success: true })
}
```

---

## 7. Generation Flow (Async)

```
generateKitAsync(kitId, inputs)
  │
  ├── [PARALLEL]
  │     ├── generatePhotos() × 5     → Imagen 3 → Vercel Blob × 5
  │     └── generateCaptions()       → Gemini Flash-Lite → DB
  │
  ├── generateVideoScript()          → Gemini Flash → stored in memory
  │
  └── generateVideo(script)          → Veo 2 → job_id returned
        │
        └── pollVeoJob(job_id)       → every 10s until done
              │
              └── storeVideo()       → Vercel Blob → DB updated
                    │
                    └── updateKitStatus("complete")
```

Photos and captions finish in ~15–30 seconds. Video finishes in 60–120 seconds. The frontend shows photos + captions as soon as they're ready, with a "video generating..." indicator for the video.

---

## 8. Frontend Flow

### Pages

```
/tools/dropkit               → Entry / landing (if accessed from dashboard)
/tools/dropkit/create        → 4-step creation wizard
/tools/dropkit/kit/[kitId]   → Results page (polls for status, shows assets)
```

### Step Wizard State

```typescript
type WizardState = {
  step: 1 | 2 | 3 | 4
  productPhoto: File | null
  productPhotoPreview: string | null
  productName: string
  productDesc: string
  vibe: "living_room" | "outdoor" | "flat_lay" | "branded_studio" | null
  avatarId: string | null
}
```

### Results Page Polling

```typescript
// Poll every 3 seconds until all assets ready
useEffect(() => {
  const interval = setInterval(async () => {
    const status = await fetch(`/api/kit-status/${kitId}`).then(r => r.json())
    
    if (status.photos?.length > 0) setPhotos(status.photos)
    if (status.captions?.length > 0) setCaptions(status.captions)
    if (status.videoUrl) {
      setVideoUrl(status.videoUrl)
      clearInterval(interval)  // stop polling when video is done
    }
  }, 3000)
  
  return () => clearInterval(interval)
}, [kitId])
```

---

## 9. Environment Variables

```bash
# Google AI
GOOGLE_AI_API_KEY=                    # Gemini Flash + Flash-Lite
GCP_PROJECT_ID=                       # Vertex AI project
GCP_LOCATION=us-central1              # Vertex AI region
GOOGLE_APPLICATION_CREDENTIALS=       # Service account JSON path (or use Workload Identity on Vercel)

# Vercel Blob
BLOB_READ_WRITE_TOKEN=                # Auto-set by Vercel Blob addon

# Database
DATABASE_URL=                         # Neon PostgreSQL connection string

# JobEscape integration
JOBESCAPE_JWT_SECRET=                 # To validate user tokens from JobEscape
JOBESCAPE_SERVICE_KEY=                # Internal service-to-service key for credit top-ups

# App
NEXT_PUBLIC_APP_URL=https://dropkit.jobescape.me
```

---

## 10. Deployment on Vercel

### Setup Steps

1. Create new Next.js project: `npx create-next-app@latest dropkit`
2. Add Vercel Blob: `vercel add blob` in project settings
3. Add Neon DB: connect via Vercel integrations → Neon
4. Set all env vars in Vercel project settings
5. Configure Google service account: upload JSON as env var or use Workload Identity
6. Deploy: `vercel --prod`

### Custom Domain

Route `dropkit.jobescape.me` (or `/tools/dropkit` subdirectory) to this Vercel project via CNAME in JobEscape's DNS settings.

### Vercel Function Limits

| Function | Timeout needed | Vercel plan required |
|---|---|---|
| `/api/generate-kit` | 30s (kicks off async) | Hobby / Pro |
| `/api/kit-status` | 5s | Any |
| Background generation | 300s (video polling) | Pro (use Vercel Cron or background job) |

For video generation polling (up to 120s), use **Vercel Pro** with max duration set to 300s, or offload to a background worker via Vercel Queues or a simple cron job that checks pending Veo jobs every 15 seconds.

---

## 11. Cost Estimates per Kit

| Operation | Model | Approx. cost |
|---|---|---|
| 5 product photos | Imagen 3 | ~$0.08 (5 × $0.016/image) |
| 3 ad captions | Gemini Flash-Lite | ~$0.001 |
| Video script | Gemini Flash | ~$0.002 |
| 1 review video (25s) | Veo 2 | ~$0.35 (est. $0.014/sec) |
| Blob storage (7 days) | Vercel Blob | ~$0.01 |
| **Total COGS per kit** | | **~$0.44** |

At 50 credits = $0.99 equivalent charge to user (based on credit pack pricing), **gross margin per kit ≈ 55%**. At scale, Imagen 3 batch pricing reduces this further.

---

## 12. Integration with JobEscape

### Auth Flow
JobEscape passes its session JWT in the `Authorization: Bearer <token>` header when embedding DropKit in an iframe or redirecting to it. DropKit validates this token using the shared `JOBESCAPE_JWT_SECRET` and extracts `userId`.

### Credit Sync
JobEscape's backend calls `POST /api/credits/add` with the service key when:
- User reaches Module 4 Lesson 3 (100 free credits granted once)
- User purchases a credit pack from JobEscape's payment flow
- Monthly credit refresh for subscribers

DropKit never processes payments directly — all billing stays in JobEscape.

### Course Trigger
In JobEscape's course renderer, Lesson 4.3 includes a `tool_cta` component that renders:
```json
{
  "type": "tool_cta",
  "tool": "dropkit",
  "label": "Create My Product Kit →",
  "url": "/tools/dropkit/create"
}
```
This passes the user's session automatically.

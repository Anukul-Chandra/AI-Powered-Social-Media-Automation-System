# 🤖📸 AI-Powered Social Media Automation & Content Engine

> An end-to-end n8n workflow that autonomously generates AI images & videos, crafts platform-optimized captions, and publishes daily content to Facebook and Instagram — fully on autopilot.

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
![OpenAI](https://img.shields.io/badge/GPT--4o-OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white)
![Gemini](https://img.shields.io/badge/Gemini%202.5%20Flash-Google%20AI-4285F4?style=for-the-badge&logo=google&logoColor=white)
![Veo](https://img.shields.io/badge/Veo%203.1-Video%20Gen-0F9D58?style=for-the-badge&logo=google&logoColor=white)
![Cloudinary](https://img.shields.io/badge/Cloudinary-CDN-3448C5?style=for-the-badge&logo=cloudinary&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Tracking-34A853?style=for-the-badge&logo=google-sheets&logoColor=white)
![Google Drive](https://img.shields.io/badge/Google%20Drive-Storage-4285F4?style=for-the-badge&logo=google-drive&logoColor=white)
![Facebook](https://img.shields.io/badge/Facebook-Publishing-1877F2?style=for-the-badge&logo=facebook&logoColor=white)
![Instagram](https://img.shields.io/badge/Instagram-Publishing-E4405F?style=for-the-badge&logo=instagram&logoColor=white)


## ✅ Project Overview :

A fully autonomous **n8n automation pipeline** that wakes up every morning at 6AM, picks the next product from Google Drive, generates a studio-quality AI image using Gemini 2.5 Flash, produces a cinematic short video using Veo 3.1, writes platform-specific captions using GPT-4o, and cross-posts to both Facebook and Instagram — then logs everything and sends a status email. **Zero human input required after initial setup.**

**What changed after deployment:**

- ✅ Daily posting on both platforms — fully automated at 6:00 AM
- ✅ AI-generated studio-quality product images — every single day
- ✅ AI-generated cinematic Reels/video posts — no video editing needed
- ✅ Smart alternating mode: FB gets Image → IG gets Video (next day flips)
- ✅ Platform-specific GPT-4o captions — under 255 characters, always on-brand
- ✅ Full audit log — every post tracked with product, index, IDs, and timestamps
- ✅ Email alerts on success and failure — immediate visibility

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     DAILY CRON TRIGGER  (6:00 AM)                       │
│                        cron: 0 0 6 * * *                                │
└─────────────────────────┬───────────────────────────────────────────────┘
                          │
              ┌───────────┴───────────┐
              ▼                       ▼
   List Product Images          Read Tracking Sheet
   (Google Drive Folder)        (content_posting_tracking)
              │                       │
              └───────────┬───────────┘
                          ▼
              ┌─────────────────────┐
              │   MERGE + COMPUTE   │  ← Determines next product index
              │  Next Index & Mode  │    and today's post mode
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  GDrive – List      │  ← Matches product image to a
              │  Product Docs       │    product description document
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Parse Product Data │  ← Extracts product name, URL,
              │  + Fetch Doc Text   │    and description from Drive doc
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │ IF: Has Doc?        │
              └────┬─────────┬──────┘
                   ▼         ▼
            Fetch Doc    GPT-4o – Generate
              Text       Description (fallback)
                   │         │
                   └────┬────┘
                        ▼
              ┌─────────────────────┐
              │ Download Product    │
              │ Image from Drive    │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ Gemini Vision –     │  ← Analyzes product image
              │ Analyze Image       │    → generates image gen prompt
              │ → Prompt            │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ Gemini 2.5 Flash –  │  ← Generates studio-quality
              │ Generate AI Image   │    AI product image
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Upload AI Image    │  → Google Drive storage
              │  to GDrive          │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │ Prepare Image       │
              │ for Veo (Base64)    │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │  Veo 3.1 –          │  ← Generates 6–8 sec cinematic
              │  Generate AI Video  │    product video from image
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Upload AI Video    │  → Google Drive storage
              │  to GDrive          │
              └──────────┬──────────┘
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
     GPT-4o –               GPT-4o –
     Generate FB             Generate IG
     Caption                Caption
              │                     │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │  Merge Captions     │
              │  + Build Post Data  │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │  IF: Mode =         │
              │  fb_image_ig_video  │
              │  OR fb_video_ig_image│
              └──────────┬──────────┘
                         │
          ┌──────────────┼──────────────────┐
          ▼                                  ▼
  [IMAGE DAY]                        [VIDEO DAY]
  FB → Image (Cloudinary)            FB → Video (Cloudinary)
  IG → Video Reel (Cloudinary)       IG → Image (Cloudinary)
          │                                  │
          └──────────────┬───────────────────┘
                         ▼
              ┌─────────────────────┐
              │  Collect All Post   │
              │  Results            │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ IF: All Posts       │
              │ Succeeded?          │
              └────┬─────────┬──────┘
                   ▼         ▼
              SUCCESS      FAILURE
                   │            │
        ┌──────────┤   ┌────────┘
        ▼          │   ▼
  Update         Log Failure
  Tracking  →  to post_logs
  Sheet          Sheet
        ▼               ▼
  Log Success     Email – Failure
  to post_logs    Notification
  Sheet
        ▼
  Email – Success
  Notification
```

---

## 🛠️ Tech Stack & Tools

| Tool | Purpose |
|---|---|
| **n8n** | Workflow orchestration engine |
| **Google Drive** | Product image & description document storage; AI asset storage |
| **Google Sheets** | Content tracking (`content_posting_tracking`) and post log (`post_logs`) |
| **OpenAI GPT-4o** | Product description generation, Facebook caption, Instagram caption |
| **Google Gemini 2.5 Flash** | AI product image generation from prompt |
| **Google Gemini Vision (gemini-3.1-flash-image-preview)** | Visual analysis of product images to create image generation prompts |
| **Google Veo 3.1** | AI cinematic short video generation from product image |
| **Cloudinary** | Media CDN — image and video upload & hosting for posting |
| **UploadPost** | Cross-platform social media posting (Facebook + Instagram) |
| **Gmail** | Success and failure notification emails |

---

## ✨ Core Features

### ⏰ Daily Scheduled Trigger

The pipeline fires automatically every morning at **6:00 AM** via a cron schedule (`0 0 6 * * *`). No manual intervention is ever required. The workflow self-manages which product to post next by reading the tracking sheet and computing the next sequential index.

---

### 📂 Smart Product Selection

Two parallel reads happen at startup:

- **Google Drive** — lists all files in the `Product Image` folder
- **Google Sheets** — reads the `content_posting_tracking` tab to find the last used image name, last post type, last post mode, and remaining image queue

A JavaScript logic block then:
- Computes which product image comes next in the rotation
- Determines the remaining image queue (avoids repeats until all are used)
- Decides today's posting mode: `fb_image_ig_video` or `fb_video_ig_image`

---

### 📄 Product Description Intelligence

After selecting the image, the workflow:

1. Lists files in the `Product Description` Google Drive folder
2. Fuzzy-matches the product image filename to a corresponding product doc
3. Fetches the full doc text via HTTP
4. Falls back to **GPT-4o** to generate a 2–3 sentence premium lifestyle description if no matching document is found

All product descriptions are written in a premium lifestyle tone appropriate for a UK cannabis brand.

---

### 🎨 AI Image Generation Pipeline

**Step 1 — Visual Analysis (Gemini Vision)**
The raw product image is downloaded from Drive, converted to Base64, and sent to `gemini-3.1-flash-image-preview` via the Google AI API. The model performs expert-level product photography analysis and outputs a detailed image generation prompt describing the product's shape, colors, packaging, lighting, and background in full.

**Step 2 — Image Generation (Gemini 2.5 Flash)**
The generated prompt is fed to `models/gemini-2.5-flash-image`, which produces a studio-quality AI-rendered product photograph. The resulting image is uploaded back to Google Drive for storage and then prepared for video generation.

---

### 🎬 AI Video Generation (Veo 3.1)

The AI-generated product image is base64-encoded and sent to **Google Veo 3.1** (`models/veo-3.1-generate-preview`) with a cinematic video prompt that specifies:

- 6–8 second luxury cinematic format
- Must visually match the source product (color, texture, shape, structure)
- Dark green, black, and gold luxury aesthetic
- UK dispensary visual style
- Minimal, premium, editorial motion

The resulting video is uploaded to Google Drive and used for either the Facebook or Instagram post depending on today's mode.

---

### 📝 GPT-4o Caption Engine

Two separate GPT-4o calls generate platform-optimized captions simultaneously:

| Platform | Caption Rules |
|---|---|
| **Facebook** | Max 255 characters · Single line · No line breaks · 2–4 word punchy hook · Premium, clean, exciting tone · Max 2 hashtags |
| **Instagram** | Max 255 characters · Single line · No line breaks · 2–4 word punchy hook · Premium UK cannabis voice · Max 2–3 hashtags |

Both captions include the product name, lifestyle description, and product link. The system enforces strict character limits to prevent truncation on either platform.

---

### 🔄 Smart Alternating Post Mode

Every day the workflow alternates the content format across platforms:

| Mode | Facebook | Instagram |
|---|---|---|
| `fb_image_ig_video` | Posts the AI image | Posts the AI video as a Reel |
| `fb_video_ig_image` | Posts the AI video | Posts the AI image |

The mode flips on every successful run, ensuring both platforms receive both content formats over time and no content type is over-represented.

---

### ☁️ Cloudinary CDN Layer

Before posting to social media, all media (images and videos) are uploaded to **Cloudinary** to obtain a reliable CDN URL. This step is required because Facebook and Instagram's APIs require publicly accessible URLs — Google Drive links are not directly compatible. Cloudinary handles both image and video resource types.

---

### 📤 Cross-Platform Posting (UploadPost)

The workflow posts to four combinations depending on the day's mode:

| Scenario | Facebook Post | Instagram Post |
|---|---|---|
| Image Day | `FB – Post Image` (photo post) | `IG – Post Video` (Reel) |
| Video Day | `FB – Post Video` (video post) | `IG – Post Image` (photo post) |

All posts carry the AI-generated platform-specific caption. Instagram Reels are posted using the `REELS` media type.

---

### 📊 Google Sheets Logging

Two structured tabs are maintained automatically in real time:

**Tab 1 — `content_posting_tracking`**

| Column | Description |
|---|---|
| `used_img_name` | Filename of the product image used |
| `next_index` | Sequential index of the post |
| `today_post_mode` | Mode used (`fb_image_ig_video` / `fb_video_ig_image`) |
| `last_post_type` | FB or IG dominant content type |
| `account_name` | Social account identifier |
| `platform` | Target platform |
| `remaining_images` | Remaining image queue (JSON array) |

**Tab 2 — `post_logs`**

| Column | Description |
|---|---|
| `index` | Post sequence index |
| `product_name` | Product name derived from image filename |
| `image_file_name` | Original image filename |
| `fb_post_type` | `image` or `video` |
| `ig_post_type` | `image` or `video` |
| `today_post_mode` | Full mode string |
| `fb_post_id` | Facebook post ID returned by API |
| `ig_post_id` | Instagram post ID returned by API |

---

### 📧 Email Notifications

After every run, a Gmail notification is sent to the configured address:

- ✅ **Success** — subject includes product name and post index; body contains FB post ID, IG post ID, captions used, and timestamps
- ❌ **Failure** — subject flags the failed product and index; body instructs the operator to fix the issue and re-run manually; tracking sheet is **not** updated on failure to preserve integrity

---

## 📁 Repository Structure

```
📦 posenigma-social-automation/
├── 📄 README.md                    ← You are here
├── 📄 PoseNigma2_Final.json        ← n8n workflow (import this)
└── 📄 .gitignore
```

---

## 🚀 Setup & Installation Guide

### Prerequisites

Before you begin, ensure you have:

- [ ] **n8n** installed (self-hosted or cloud at [n8n.io](https://n8n.io))
- [ ] **Google** account with Drive and Sheets access
- [ ] **OpenAI API** account with GPT-4o access
- [ ] **Google AI (Gemini + Veo)** API key with access to `gemini-2.5-flash-image` and `veo-3.1-generate-preview`
- [ ] **Cloudinary** account (free tier works)
- [ ] **UploadPost** account connected to Facebook Page and Instagram Business account
- [ ] **Gmail** account for notification emails

---

### Step 1 — Import the Workflow

1. Open your **n8n dashboard**
2. Go to **Workflows → Import from File**
3. Upload `PoseNigma2_Final.json`
4. The workflow loads with all 55 nodes — **do not activate yet**

---

### Step 2 — Google Drive Structure

Create two folders in Google Drive:

**Folder 1 — `Product Image`**
Upload all product images here. Name each file descriptively:
```
1_Blue-Dream-Cartridge.jpg
2_OG-Kush-Flower.jpg
3_Gelato-Pre-Roll.jpg
```
The filename (without the number prefix and extension) becomes the product name.

**Folder 2 — `Product Description`**
Create Google Docs (or plain text files) with product descriptions. The workflow fuzzy-matches filenames from the image folder to find the corresponding doc.

Copy the **folder IDs** from the Drive URLs and replace:

| Placeholder | Replace With |
|---|---|
| `YOUR_PRODUCT_IMAGE_FOLDER_ID` | Drive folder ID for product images |
| `YOUR_PRODUCT_DESC_FOLDER_ID` | Drive folder ID for product descriptions |

---

### Step 3 — Google Sheets Setup

Create a Google Sheet named **`Social Media Automation`** with two tabs:

**Tab 1 — `content_posting_tracking`**
```
used_img_name | account_name | platform | today_post_mode | last_post_type | next_index | remaining_images | row_number
```
Add one starter row with:
- `account_name`: your social account name (e.g. `thebitz420dotio`)
- `platform`: `instagram`
- `today_post_mode`: `fb_video_ig_image` (so first run flips to `fb_image_ig_video`)
- `next_index`: `0`
- `remaining_images`: `[]`

**Tab 2 — `post_logs`**
```
index | product_name | image_file_name | fb_post_type | ig_post_type | today_post_mode | fb_post_id | ig_post_id
```
Leave this empty — the workflow populates it automatically.

Copy your **Sheet ID** from the URL:
```
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_HERE/edit
```

Replace `YOUR_GOOGLE_SHEET_ID` in all Google Sheets nodes.

---

### Step 4 — Google Credentials (Drive, Sheets, Gmail)

1. In n8n → **Credentials → New → Google Drive OAuth2** → authorize
2. In n8n → **Credentials → New → Google Sheets OAuth2** → authorize
3. In n8n → **Credentials → New → Gmail OAuth2** → authorize
4. Assign each credential to the corresponding nodes in the workflow

---

### Step 5 — OpenAI Credential

1. Go to [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Create a new **API Key**
3. In n8n → **Credentials → New → OpenAI** → paste API key
4. Assign to all three **OpenAI** nodes:
   - `GPT-4o – Generate Description`
   - `Generate FB Caption`
   - `Generate IG Caption5`

> ⚠️ Ensure your OpenAI plan has access to **gpt-4o**.

---

### Step 6 — Google AI (Gemini + Veo) Credential

1. Go to [Google AI Studio](https://aistudio.google.com) → **Get API Key**
2. In n8n → **Credentials → New → Google Gemini(PaLM) API**
3. Paste your API key
4. Assign to:
   - `Gemini – Generate AI Image` (uses `models/gemini-2.5-flash-image`)
   - `Veo 3 – Generate AI Video` (uses `models/veo-3.1-generate-preview`)

The `Gemini – Analyze Image → Prompt` node uses this key via an HTTP Request node — replace `YOUR_GEMINI_API_KEY` in the header of that node.

> ⚠️ Ensure your Google AI API key has access to **Gemini 2.5 Flash Image** and **Veo 3.1** (these may require allowlist access via Google AI Studio).

---

### Step 7 — Cloudinary Credential

1. Sign up at [cloudinary.com](https://cloudinary.com) and go to your **Dashboard**
2. Copy **Cloud Name**, **API Key**, and **API Secret**
3. In n8n → **Credentials → New → Cloudinary API** → fill in all three values
4. Assign to all four Cloudinary nodes:
   - `Cloudinary – Upload FB Image`
   - `Cloudinary – Upload FB Video (Video Day)`
   - `Cloudinary – Upload IG Video2 (Image Day)`
   - `Cloudinary – Upload IG Image (Video Day)`

---

### Step 8 — UploadPost Credential

1. Sign up at [uploadpost.io](https://uploadpost.io) and connect your Facebook Page and Instagram Business account
2. In n8n → **Credentials → New → UploadPost API**
3. Paste your API key
4. In each `uploadPost` node, verify:
   - `user` field matches your UploadPost account username
   - `facebookPageId` matches your Facebook Page ID

Replace these placeholders in the posting nodes:

| Placeholder | Replace With |
|---|---|
| `YOUR_UPLOADPOST_USERNAME` | Your UploadPost username |
| `YOUR_FACEBOOK_PAGE_ID` | Your Facebook Page numeric ID |

---

### Step 9 — Email Notification Address

In both the `Email – Success Notification2` and `Email – Failure Notification2` Gmail nodes, replace the `sendTo` address:

| Placeholder | Replace With |
|---|---|
| `YOUR_NOTIFICATION_EMAIL@gmail.com` | Email address to receive post alerts |

---

### Step 10 — Activate

1. Double-check all credentials are assigned (no red warning icons on any node)
2. Click **Save**
3. Toggle the workflow **Active**
4. The workflow will fire automatically at **6:00 AM** daily
5. To test immediately: click **Execute Workflow** manually from the n8n editor
6. Monitor results in the **Executions** tab and check your Google Sheet

---

## 🔄 End-to-End Flow Summary

```
⏰ 6:00 AM — Cron fires
     ↓ List product images from Drive + read tracking sheet
     ↓ Compute next product index & today's post mode
     ↓ Match product image to description doc
     ↓ Download product image from Drive
     ↓ Gemini Vision analyzes image → generates image prompt
     ↓ Gemini 2.5 Flash generates AI product image
     ↓ Veo 3.1 generates AI cinematic product video
     ↓ GPT-4o writes FB caption (≤255 chars)
     ↓ GPT-4o writes IG caption (≤255 chars)
     ↓ Mode check: fb_image_ig_video OR fb_video_ig_image?
     ↓ Cloudinary uploads image & video as CDN assets
     ↓ UploadPost → Facebook (image or video)
     ↓ UploadPost → Instagram (Reel or image)
     ↓ Collect post results
     ↓ Success? → Update tracking sheet + log to post_logs
     ↓ Gmail → success or failure notification sent
```

---

## ⚙️ Customization

### Change the Posting Time
In the **Daily Cron Trigger** node, update the cron expression:
```
0 0 6 * * *   → fires at 6:00 AM daily
0 0 9 * * *   → fires at 9:00 AM daily
0 0 18 * * *  → fires at 6:00 PM daily
```

### Change the Post Mode Starting Point
In the `content_posting_tracking` sheet, update the `today_post_mode` value in the tracker row. The next run will flip from whatever value is stored:
```
fb_image_ig_video  → next run becomes fb_video_ig_image
fb_video_ig_image  → next run becomes fb_image_ig_video
```

### Modify Caption Rules
In `Generate FB Caption` or `Generate IG Caption5` nodes, edit the system prompt. Key rules are enforced inline in the prompt text. You can change tone, character limit, hashtag count, or brand voice directly.

### Modify the Video Prompt Style
In the `Veo 3 – Generate AI Video` node, edit the prompt field. The current style targets a UK luxury dispensary aesthetic with dark green/black/gold palette. Swap this for your own brand aesthetic.

### Add More Product Images
Drop new images into the `Product Image` Google Drive folder. The rotation logic automatically picks them up on the next cycle once the current queue is exhausted. Create a matching doc in `Product Description` for best results.

---

## 🔐 Security Best Practices

- ✅ Never commit the workflow JSON with real API keys or credentials embedded
- ✅ Remove or replace the Gemini API key visible in the `Gemini – Analyze Image → Prompt` HTTP Request node before pushing to any repository
- ✅ Use n8n environment variables for all sensitive values where possible
- ✅ Rotate API keys periodically — especially Gemini, OpenAI, and Cloudinary
- ✅ Keep your n8n instance behind authentication and a firewall
- ✅ Revoke and regenerate the UploadPost API key if the workflow JSON is ever exposed

---

## 📝 Full Environment Variables Reference

| Placeholder | Description |
|---|---|
| `YOUR_GDRIVE_CREDENTIAL_ID` | n8n Google Drive OAuth2 credential ID |
| `YOUR_GSHEETS_CREDENTIAL_ID` | n8n Google Sheets OAuth2 credential ID |
| `YOUR_GMAIL_CREDENTIAL_ID` | n8n Gmail OAuth2 credential ID |
| `YOUR_OPENAI_CREDENTIAL_ID` | n8n OpenAI credential ID |
| `YOUR_GEMINI_CREDENTIAL_ID` | n8n Google Gemini API credential ID |
| `YOUR_GEMINI_API_KEY` | Raw Gemini API key (used in HTTP Request node header) |
| `YOUR_CLOUDINARY_CREDENTIAL_ID` | n8n Cloudinary API credential ID |
| `YOUR_UPLOADPOST_CREDENTIAL_ID` | n8n UploadPost API credential ID |
| `YOUR_GOOGLE_SHEET_ID` | Google Sheet document ID (from URL) |
| `YOUR_PRODUCT_IMAGE_FOLDER_ID` | Google Drive folder ID for product images |
| `YOUR_PRODUCT_DESC_FOLDER_ID` | Google Drive folder ID for product description docs |
| `YOUR_FACEBOOK_PAGE_ID` | Facebook Page numeric ID |
| `YOUR_UPLOADPOST_USERNAME` | UploadPost account username |
| `YOUR_NOTIFICATION_EMAIL@gmail.com` | Email address for success/failure alerts |

---

## 📄 License

This project is proprietary and intended for internal deployment. Not for public redistribution.

---low saved you time, consider starring the repo!

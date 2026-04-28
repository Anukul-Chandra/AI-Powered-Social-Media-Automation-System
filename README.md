# 🤖 AI-Powered Social Media Automation System

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

---

## 📋 Table of Contents

- [Problem Statement](#-problem-statement)
- [Solution Overview](#-solution-overview)
- [System Architecture](#-system-architecture)
- [Tech Stack & Tools](#-tech-stack--tools)
- [Core Features](#-core-features)
- [Setup & Installation Guide](#-setup--installation-guide)

---

## 🔴 Problem Statement

Managing a consistent social media presence for a product-based brand is a resource-intensive process. The typical daily workflow requires:

- 🖼️ **Manually selecting** product images and writing platform-specific captions for Facebook and Instagram.
- 🎨 **Generating creative AI visuals** and short-form videos for each product individually.
- 🔀 **Switching between tools** (image editors, caption writers, schedulers, CDN uploaders) to post content.
- 📉 **Tracking post status** across platforms and following up on failures manually.

This fragmented, time-consuming process makes it near-impossible to post high-quality, consistent content daily — especially across two platforms with different format requirements (image vs. video).

---

## ✅ Solution Overview

**PoseNigma** is a fully automated n8n workflow that eliminates all of the above. Triggered daily by a cron job, it:

1. 📊 Picks the next unposted product from a **Google Sheets tracking sheet**.
2. 📁 Fetches the product image and documentation from **Google Drive**.
3. 🧠 Uses **GPT-4o** to generate a product description and platform-tailored captions for Facebook and Instagram.
4. 🎨 Uses **Gemini 2.5 Flash** to generate a unique AI image, and **Veo 3** to generate a short AI video.
5. ☁️ Uploads assets to **Cloudinary** and publishes to **Facebook and Instagram** in the correct format.
6. 📬 Logs success or failure back to **Google Sheets** and sends an **email notification** via Gmail.

The workflow runs entirely hands-free — zero human intervention required after initial setup.

---

## 🏗 System Architecture

The workflow is divided into four major pipeline stages:

```
┌─────────────────────────────────────────────────────────────────┐
│                     STAGE 1: DATA INGESTION                      │
│  Daily Cron → Google Sheets (Tracking) → Google Drive (Images)   │
│              → Google Drive (Product Docs) → Merge Data           │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                   STAGE 2: CONTENT GENERATION                    │
│  GPT-4o (Description) → GPT-4o (FB Caption) → GPT-4o (IG Cap.)  │
│  Gemini Vision (Image Analysis) → Gemini Flash (AI Image Gen.)   │
│                       → Veo 3 (AI Video Gen.)                    │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                    STAGE 3: CONTENT POSTING                      │
│  Determine Day Type (Image Day / Video Day)                      │
│  → FB: Image or Video  |  IG: Video or Image (cross-format)     │
│  → Cloudinary Upload → UploadPost API → Platform Publish        │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                  STAGE 4: TRACKING & NOTIFICATION                │
│  Collect All Post Results → Success / Failure Check             │
│  → Update Google Sheets → Log to post_log Sheet                 │
│  → Gmail Email Notification (Success or Failure)                │
└─────────────────────────────────────────────────────────────────┘
```

### 🔢 Node Count: 55 nodes across 4 pipeline stages

---

## 🛠 Tech Stack & Tools

| Category | Tool / Service |
|---|---|
| ⚙️ **Workflow Automation** | [n8n](https://n8n.io) |
| 🧠 **AI — Text & Captions** | OpenAI GPT-4o |
| 🎨 **AI — Image Generation** | Google Gemini 2.5 Flash (Image) |
| 🎬 **AI — Video Generation** | Google Veo 3.1 |
| 👁️ **AI — Image Analysis** | Google Gemini Vision (HTTP API) |
| 📁 **Product Data Storage** | Google Drive |
| 📊 **Tracking & Logging** | Google Sheets |
| ☁️ **CDN / Asset Hosting** | Cloudinary |
| 📣 **Social Publishing** | UploadPost API (Facebook & Instagram) |
| 📬 **Notifications** | Gmail (via n8n Gmail node) |
| 💻 **Scripting** | JavaScript (n8n Code nodes) |

---

## ⚡ Core Features

### 🗂️ Intelligent Product Queue
Reads a Google Sheets tracking sheet to determine the **next unposted product**. Fetches the corresponding product image and optional product document from Google Drive. Skips products that have already been posted, preventing duplicate content.

### 🧠 AI-Powered Content Generation
**GPT-4o** generates a rich product description (optionally enhanced with document text if a product doc exists in Drive). GPT-4o then generates separate, platform-optimized captions for Facebook and Instagram. **Gemini Vision** analyzes the real product image and constructs a detailed prompt. **Gemini 2.5 Flash** uses that prompt to generate a high-quality **AI image**. **Veo 3.1** generates a short **AI video** for video-day posts.

### 📅 Alternating Format Strategy
The workflow intelligently alternates posting format across platforms each day:

| Day Type | Facebook | Instagram |
|---|---|---|
| 🖼️ **Image Day** | Posts AI Image | Posts AI Video |
| 🎬 **Video Day** | Posts AI Video | Posts AI Image |

This keeps the content format varied and engaging across both platforms.

### ☁️ Cloudinary CDN Pipeline
All AI-generated images and videos are uploaded to **Cloudinary** before posting. Cloudinary handles optimization, transformation, and reliable public URLs for the publishing API.

### 📊 Automated Tracking & Logging
On success: updates the tracking sheet (marks product as posted) and logs to the `post_log` sheet. On failure: logs the error to `post_log` and sends a failure notification email. Email notifications via **Gmail** for both success and failure outcomes.

### 🛡️ Robust Error Handling
Separate success and failure branches with dedicated logging. Conditional nodes (`IF` nodes) check document availability, post results, and content type before proceeding. Graceful handling of missing product documents — falls back to image-only description generation.

---

## 🚀 Setup & Installation Guide

### 📋 Prerequisites

Before importing this workflow, make sure you have the following ready:

- ✅ A running **n8n instance** (self-hosted or cloud)
- ✅ API access to: **OpenAI**, **Google Cloud (Gemini + Veo)**, **Cloudinary**, **UploadPost**
- ✅ A **Google Workspace** account with Drive and Sheets access
- ✅ A **Gmail** account for sending notifications
- ✅ Facebook & Instagram accounts connected via **UploadPost**

---

### 📥 Step 1: Clone or Download the Workflow

```bash
git clone https://github.com/YOUR_USERNAME/PoseNigma.git
```

Or download `PoseNigma2_Final.json` directly.

---

### 📂 Step 2: Import into n8n

1. Open your n8n instance.
2. Go to **Workflows → Import from File**.
3. Select `PoseNigma2_Final.json`.
4. The workflow will appear with all 55 nodes pre-configured.

---

### 🔑 Step 3: Configure Credentials

In n8n, go to **Settings → Credentials** and add the following:

| Credential | Used By |
|---|---|
| 🔐 **Google OAuth2** | Google Drive, Google Sheets, Gmail |
| 🤖 **OpenAI API Key** | GPT-4o nodes |
| 🌟 **Google Gemini API Key** | Gemini image + Veo 3 video nodes |
| ☁️ **Cloudinary API** | Cloudinary upload nodes |
| 📣 **UploadPost API** | Facebook & Instagram posting nodes |
| 🔗 **Google API Key (HTTP)** | Gemini Vision HTTP Request node |

> ⚠️ Make sure each credential is assigned to the correct node after import. n8n may show credential warnings — click each node and re-select your credentials.

---

### 📊 Step 4: Set Up Google Sheets

Create a Google Sheet with the following structure:

**`tracking_sheet` tab:**

| Column | Description |
|---|---|
| `product_name` | Name of the product |
| `image_file_id` | Google Drive file ID of the product image |
| `doc_file_id` | (Optional) Google Drive file ID of the product doc |
| `posted` | Leave blank for unposted; set to `TRUE` after posting |
| `post_date` | Auto-filled by the workflow |
| `status` | Auto-filled (`success` or `failed`) |

**`post_log` tab:** Used automatically by the workflow to log all post results.

---

### 📁 Step 5: Set Up Google Drive Folders

Create the following folder structure in your Google Drive:

```
📁 PoseNigma/
├── 📁 Product Images/       ← Upload product images here
├── 📁 Product Docs/         ← (Optional) Upload product PDFs or docs here
├── 📁 AI Generated Images/  ← Workflow uploads AI images here
└── 📁 AI Generated Videos/  ← Workflow uploads AI videos here
```

Copy the **Folder IDs** from the Drive URLs and update the corresponding Google Drive nodes in the workflow.

---

### ⏰ Step 6: Configure the Cron Trigger

1. Open the **Daily Cron Trigger** node.
2. Set the schedule to your preferred posting time (e.g., `9:00 AM` daily).
3. Adjust the timezone to match your target audience.

---

### 📣 Step 7: Update UploadPost Node Settings

1. Open each **UploadPost** node (FB Image, FB Video, IG Image, IG Video).
2. Select your connected **Facebook Page** and **Instagram Account**.
3. Verify the caption fields are mapped from the `Generate FB Caption` / `Generate IG Caption` nodes.

---

### ▶️ Step 8: Activate the Workflow

1. Toggle the workflow to **Active** in the top-right of the n8n editor.
2. Optionally, do a **manual test run** by clicking "Test Workflow" to verify the full pipeline with one product.
3. Check your Google Sheets `post_log` and Gmail inbox for results.

---

### 📈 Step 9: Monitor & Scale

- ➕ Add more products to your `tracking_sheet` at any time — the workflow picks up the next unposted one each day.
- 🔍 Review the `post_log` sheet weekly to monitor success rates.
- ✏️ Adjust the GPT-4o prompt inside the caption nodes to match your brand voice.

---

## 📁 File Structure

```
PoseNigma/
├── PoseNigma2_Final.json    # Main n8n workflow export
└── README.md                # This file
```

---

## 📄 License

This project is licensed under the **MIT License** — feel free to use, modify, and distribute.

---

## 🙋‍♂️ Author

Built and maintained by the PoseNigma team. For questions, open an issue or reach out via the repository.

---

> ⭐ If this workflow saved you time, consider starring the repo!

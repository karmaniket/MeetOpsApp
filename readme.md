# MeetOps - Automate Your Meeting Operations

MeetOps is an AI-powered meeting automation system that processes raw transcripts, extracts structured action items with owners and deadlines, and delivers them to your team via Slack or Discord, all in seconds. Powered by Google's Gemini model, it converts unstructured meeting conversations into executable workflows, reducing manual coordination overhead.

# *[Live](https://meetopsai.vercel.app)*

## Demo

[![Demo](https://img.youtube.com/vi/d7Nvl2-KT14/maxresdefault.jpg)](https://youtu.be/d7Nvl2-KT14)

## Features

- AI transcript cleaning, removes filler words, fixes typos, preserves speaker structure
- Action item extraction with owner, due date, and priority (HIGH / MEDIUM / LOW)
- One-click delivery to Slack or Discord via webhook
- Live global usage counter visible to all users
- Persistent feedback system powered by Supabase
- 15-minute session auto-clear for privacy
- API rate limiter
- Gemini API limit notification on Discord via webhook
- Community feedback page with social handle verification and platform linking

## Architecture

```
meetops/
├── backend/
│   ├── agents/
│   │   ├── pipeline.py        # Ingestion, extraction, execution agents
│   │   └── logger.py          # Operational logging
│   ├── tools/
│   │   ├── discord_tool.py    # Slack and Discord webhook integration
│   │   └── calendar_tool.py   # Calendar event scaffolding
│   ├── main.py                # FastAPI app entry point
│   └── requirements.txt
│
└── frontend/
    ├── app/
    │   ├── page.tsx            # Main page
    │   ├── layout.tsx          # Root layout and metadata
    │   ├── globals.css         # Global styles and theme
    │   └── api/
    │       ├── feedback/       # Supabase feedback GET + POST
    │       ├── webhook/        # Server-side Slack/Discord relay
    │       └── stats/          # Global usage counter
    ├── components/
    │   ├── Hero.tsx
    │   ├── UploadCard.tsx
    │   ├── Results.js
    │   ├── WebhookSender.js
    │   ├── FeedbackTicker.js
    │   ├── FeedbackSection.tsx
    │   ├── FAQ.tsx
    │   ├── Footer.js
    │   └── Toast.js
    └── lib/
        ├── supabase.ts
        └── types.ts
```

## Tech Stack

```
|       Layer         |       Technology          |
|         ---         |           ---             |
| AI Model            | Google Gemini 2.5 Flash   |
| Backend             | FastAPI + Uvicorn         |
| Frontend            | Next.js 14 + Tailwind CSS |
| Database            | Supabase (PostgreSQL)     |
| Fonts               | Syne + DM Sans            |
| Hosting - Backend   | Render                    |
| Hosting - Frontend  | Vercel                    |
```

## Setup

### Backend

```bash
git clone https://github.com/karmaniket/MeetOps.git
cd MeetOps
pip install -r meetops/requirements.txt
```

Create a `.env` file inside `meetops/`:
```
GEMINI_API_KEY=your_key
ALERT_DISCORD_WEBHOOK_URL=your_discord_webhook
```

Run locally:
```bash
uvicorn meetops.main:app --reload
```

### Frontend

1. Go to [nodejs.org](https://nodejs.org) > Download **LTS version** (v20 or higher) > Run the installer > keep all defaults

2. Open a **new** terminal and verify:
```
node -v   # should show v20.x.x
npm -v    # should show 10.x.x
```

2. Install dependencies

```bash
cd frontend
npm install
cp .env.local .env.local.example
```

Fill in `.env.local`:
```
NEXT_PUBLIC_API_URL=your_backend_url
NEXT_PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
```

Run locally:
```bash
npm run dev
```

Open `http://localhost:3000`


## Deployment

### Backend - Render

```yaml
services:
  - type: web
    name: MeetOps
    runtime: python
    repo: https://github.com/karmaniket/MeetOps
    plan: free
    region: oregon
    buildCommand: pip install -r meetops/requirements.txt
    startCommand: uvicorn meetops.main:app --host 0.0.0.0 --port $PORT
    envVars:
      - key: GEMINI_API_KEY
        sync: false
      - key: ALERT_DISCORD_WEBHOOK_URL
        sync: false
```

### Frontend - Vercel

1. Push to GitHub:
   ```bash
   git init
   git add .
   git commit -m "initial"
   git remote add origin {repo_path}
   git push -u origin main
   ```

2. Import repo at [vercel.com](https://vercel.com). Add the three `NEXT_PUBLIC_*` environment variables in the Vercel dashboard. Every subsequent `git push` auto-redeploys.

## Supabase Tables

**`feedback`**
```sql
| Column        | Type        | Notes                        |
|     ---       |     ---     |     ---                      |
| id            | uuid        | primary key, auto            |
| name          | text        | public                       |
| message       | text        | public                       |
| social_handle | text        | public, unique per platform  |
| platform      | text        | public                       |
| created_at    | timestamptz | auto                         |
```

Unique constraint: `(social_handle, platform)` combined.

**`meetings_log`**
```sql
| Column       | Type        | Notes   |
|     ---      |     ---     |   ---   |
| id           | uuid        | auto    |
| processed_at | timestamptz | auto    |
```

Both tables require RLS enabled with `allow public insert` and `allow public select` policies.

## Agents

- **Ingestion Agent:** cleans raw transcript, removes filler words, preserves speaker attribution
- **Action Agent:** extracts tasks, owners, due dates, and priorities using Gemini with the current system date as reference
- **Execution Agent:** processes extracted actions and returns structured results with metrics

## Support

MeetOps runs on free-tier infrastructure. If it saves you time, consider supporting its development:

- [Patreon](https://www.patreon.com/c/AVmades)
- [Buymeacoffee](https://buymeacoffee.com/avmades)

## License

[MIT License](LICENSE)

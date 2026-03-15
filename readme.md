# MeetOps Agent

MeetOps Agent is an AI-powered meeting automation system that processes raw transcripts, extracts structured action items with owners and deadlines, and automates follow-ups via Discord and calendar integrations. Powered by Google’s Gemini model, it converts unstructured conversations into executable workflows, reducing manual coordination and enabling faster operational feedback cycles.

## *Live On Render [link](https://meetopsapp.onrender.com)*

## *Click below for full tutorial*

[![Demo](https://img.youtube.com/vi/K6lmfH7ocXQ/maxresdefault.jpg)](https://youtu.be/K6lmfH7ocXQ)

## Table of Contents
- [Features](#features)
- [Architecture](#architecture)
- [Setup](#setup)
- [Deployment Configuration](#deployment-configuration)
- [Database](#database)
- [Agents](#agents)
- [Tools Integration](#tools-integration)
- [Logging](#logging)
- [Support MeetOpsApp](#support-meetopsapp)
- [License](#license)

## Features
- Automated meeting transcript cleaning and action extraction using AI
- Feedback and review management
- Production and marketing planning support
- Discord and calendar integration for notifications and scheduling
- Centralized logging and monitoring
- Extensible agent and tool architecture

## Architecture

```bash
meetops/
├── agents/          # Core agent logic
│  ├── __init__.py
│  ├── logger.py
│  └── pipeline.py
├── db/              # Database models and access
│  ├── __init__.py
│  ├── models.py
│  └── meetops.db
├── logs/            # Log file
│  └── agent_logs.txt
├── tools/           # Integrations
│  ├── __init__.py
│  ├── calendar_tool.py
│  └── discord_tool.py
├── __init__.py
├── .env             # API keys
├── app.py           # Main application entry point
├── main.py          # Alternate entry point or CLI
└── requirements.txt # Python dependencies
```

## Setup
1. **Clone the repository**
   ```bash
   git clone https://github.com/karmaniket/MeetOpsAgent.git
   cd MeetOpsAgent
   ```
2. **Install dependencies**
   ```bash
   pip install -r meetops/requirements.txt
   ```
3. **Configure environment**
   - Set up required environment variables (API keys for Discord, calendar integrations).
   - Update database connection settings in `db/models.py` if needed.

### Running the Project Locally
- **Run the Streamlit frontend:**
  ```bash
  streamlit run meetops/app.py
  ```
- **Run the FastAPI backend:**
  ```bash
  uvicorn meetops.main:app --reload
  ```
- **Logs: Application logs are stored in `meetops/logs/agent_logs.txt`.**

## Deployment Configuration
### Service 1: MeetOpsAgent
```bash
version: "1"
services:
- type: web
  name: MeetOpsAgent
  runtime: python
  repo: https://github.com/karmaniket/MeetOpsAgent
  plan: free
  envVars:
  - key: GEMINI_API_KEY
    sync: false
  region: oregon
  buildCommand: pip install -r meetops/requirements.txt
  startCommand: uvicorn meetops.main:app --host 0.0.0.0 --port $PORT
  autoDeployTrigger: commit
```
### Service 2: MeetOpsApp

```bash
version: "1"
services:
- type: web
  name: MeetOpsApp
  runtime: python
  repo: https://github.com/karmaniket/MeetOpsAgent
  plan: free
  envVars:
  - key: GEMINI_API_KEY
    sync: false
  region: oregon
  buildCommand: pip install -r meetops/requirements.txt
  startCommand: streamlit run meetops/app.py --server.port $PORT
  autoDeployTrigger: commit
```
<!-- > [!IMPORTANT]  
> ***Both services must be running on Render. If the API returns this error:*** <br>
> ![image](error429.png)
> ***Send a request to the following endpoint to wake the service:***
> ```bash
>  https://meetopsagent.onrender.com/
> ``` -->

## Database
- SQLite database file: `meetops.db`
- Models defined in `db/models.py`
- Stores feedback, reviews, planning data, and operational metrics.

## Agents
- **Logger Agent:** Handles logging and monitoring.
- **Pipeline Agent:** Orchestrates transcript cleaning, action extraction, and task assignment.

## Tools Integration
- **Calendar Tool:** Automates scheduling and reminders.
- **Discord Tool:** Integrates with Discord for team communications and notifications.

## Logging
- All operational logs are stored in `logs/agent_logs.txt` for monitoring and debugging.

## Support MeetOpsApp

MeetOpsApp is independently built and currently runs on free-tier infrastructure. To scale the backend, improve reliability, and handle higher meeting volumes, cloud and infrastructure upgrades are required.

**Support On [Patreon](https://www.patreon.com/c/AVmades) or [Buymeacoffee](https://buymeacoffee.com/avmades)**

If MeetOpsApp saves you time or adds value to your workflow, consider supporting its development. Your contribution directly funds cloud infrastructure, backend scaling, and continued independent development.

## License
This project is licensed under the [MIT License](LICENSE).
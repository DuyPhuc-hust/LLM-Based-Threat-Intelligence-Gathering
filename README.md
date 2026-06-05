# LLM-Based Threat Intelligence Gathering

An educational Cyber Threat Intelligence (CTI) platform that collects, enriches, analyzes, and visualizes threat intelligence from public OSINT sources. The system supports automated threat monitoring, intelligence prioritization, evidence-based reporting, and analyst-oriented dashboards.

---

## Overview

This project was developed as a cybersecurity and penetration testing capstone project. The platform demonstrates how publicly available threat intelligence can be collected, normalized, enriched, and transformed into actionable intelligence for defensive security operations.

The system integrates multiple threat intelligence sources, extracts security-related entities, correlates findings, and provides analyst dashboards for monitoring emerging threats.

---

## Key Features

### Data Collection

The platform collects threat intelligence from public sources including:

* RSS security news feeds
* CISA Known Exploited Vulnerabilities (KEV)
* Reddit cybersecurity communities
* X (Twitter) public search API
* PhishTank phishing feeds
* AlienVault OTX threat intelligence feeds
* URLhaus malware URL feeds
* ThreatFox IOC feeds
* GitHub Security Advisories
* FIRST EPSS enrichment service

Fallback datasets are provided for educational demonstrations when live APIs are unavailable.

### Threat Intelligence Processing

The platform performs:

* Data cleaning and normalization
* Duplicate detection and removal
* Entity extraction
* IOC extraction
* Threat enrichment
* Trend analysis
* Threat prioritization
* Evidence-based reporting

Supported extracted entities include:

* CVEs
* IP addresses
* Domains
* URLs
* File hashes
* MITRE ATT&CK technique IDs

### Storage and Analysis

The system supports:

* SQLite intelligence storage
* Relationship tracking
* OSINT coverage scoring
* Reliability assessment
* Threat prioritization

### Analyst Interfaces

#### React Command Center

Features include:

* Mission Control dashboard
* Priority Queue
* Threat trend visualization
* OSINT coverage metrics
* Intelligence report review

#### Streamlit Dashboard

A lightweight backup dashboard for demonstrations and development purposes.

---

## Project Architecture

```text
Public Sources
      │
      ▼
 Collection Layer
      │
      ▼
 Normalization Layer
      │
      ▼
 Entity Extraction
      │
      ▼
 Threat Enrichment
      │
      ▼
 Analysis & Prioritization
      │
      ▼
 SQLite Storage
      │
      ▼
 Analyst Dashboard & Reports
```

---

## Project Structure

```text
cti_pipeline/
├── collectors/      Public-source collectors
├── extractors/      Entity extraction and normalization
├── analysis/        Prioritization and OSINT coverage scoring
├── storage/         SQLite storage adapters
├── reports/         Intelligence reporting
├── api/             FastAPI backend

frontend/            React analyst dashboard
dashboard/           Streamlit dashboard
config/              Source configuration
docs/                Documentation
scripts/             Automation scripts
tests/               Unit tests
```

---

## System Requirements

* Python ≥ 3.10 (recommended: Python 3.10 or Python 3.11)
* Node.js ≥ 18.x
* npm ≥ 9.x

---

## Installation

### Clone Repository

```bash
git clone https://github.com/DuyPhuc-hust/LLM-Based-Threat-Intelligence-Gathering.git
cd LLM-Based-Threat-Intelligence-Gathering
```

### Create Virtual Environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Configuration

Create a `.env` file in the project root directory:

```env
X_BEARER_TOKEN=
OTX_API_KEY=
GITHUB_TOKEN=
ABUSECH_AUTH_KEY=
PHISHTANK_APP_KEY=

REDDIT_CLIENT_ID=
REDDIT_CLIENT_SECRET=
REDDIT_USER_AGENT="pentest-capstone-cti-pipeline/0.1 by your_username"

LLM_PROVIDER=
LLM_MODEL=
LLM_BASE_URL=
LLM_API_KEY=
```

---

## Running the Platform

### Start Backend

```bash
python3 -m uvicorn cti_pipeline.api.main:app --host 127.0.0.1 --port 8000 --reload
```

Backend API:

```text
http://127.0.0.1:8000
```

### Start Frontend

Open a second terminal:

```bash
cd frontend
npm install
npm run dev -- --host 127.0.0.1 --port 5173
```

### Access Dashboard

Open:

```text
http://127.0.0.1:5173
```

### Start Live Collection

To begin collecting live threat intelligence:

1. Open the dashboard.
2. Verify the backend service is running.
3. Click **Run Live Update**.
4. The system will collect data from configured OSINT and threat intelligence sources.
5. Collected data will be processed through extraction, enrichment, prioritization, and reporting modules.
6. Updated results will be displayed automatically on the dashboard.

---

## Live Pipeline Execution

A complete live collection workflow can also be executed directly from the command line:

```bash
python -m cti_pipeline.cli run-pipeline \
  --source all \
  --days 3650 \
  --fresh \
  --live-only \
  --enrich-limit 8
```

This command:

* Collects data from configured live sources
* Disables educational fallback datasets
* Enriches collected threat indicators
* Regenerates intelligence reports
* Updates dashboard data

---

## LLM-Assisted Reporting

The platform supports optional LLM-based intelligence reporting through OpenAI-compatible APIs.

Example Gemini configuration:

```bash
export LLM_PROVIDER=openai_compatible
export LLM_MODEL=gemini-2.5-flash
export LLM_BASE_URL=https://generativelanguage.googleapis.com/v1beta/openai/chat/completions
export LLM_API_KEY=<your_api_key>
```

Generate intelligence reports:

```bash
python -m cti_pipeline.cli llm-report --days 3650
```

Generated reports require:

* Evidence references
* Source attribution
* Defensive recommendations
* Confidence caveats

---

## Safety Scope

This project is strictly educational and defensive.

The platform:

* Collects information only from public sources
* Uses official APIs, RSS feeds, and documented datasets
* Does not perform private scraping
* Does not download or execute malware
* Does not perform automated exploitation
* Preserves source attribution and evidence references
* Supports defensive cybersecurity research only

The project is not intended for offensive security operations or unauthorized activities.

---

## License

This project is intended for educational and research purposes.

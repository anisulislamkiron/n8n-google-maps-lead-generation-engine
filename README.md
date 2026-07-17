# n8n-google-maps-lead-generation-engine
Enterprise B2B Lead Generation Engine: Automated Pipeline Built via n8n, Google Places API v1, JavaScript Deduplication, and Telegram Ops Monitoring
A production-grade asynchronous data pipeline built in n8n that automates localized B2B lead generation. Features dynamic Google Sheets campaign management, paginated Google Places API parsing, an optimized JavaScript deduplication logic core, and automated DevOps alerts via Telegram.

Loom Video Link- https://www.loom.com/share/a3911b989bcc43a9ae70b31c1cab3a30

# Enterprise B2B Lead Generation Engine: Automated Pipeline Built via n8n, Google Places API, and JavaScript Engine

[![n8n Version](https://img.shields.io/badge/n8n-v1.0+-ff6f61.svg?style=flat-square)](https://n8n.io/)
[![API](https://img.shields.io/badge/Google%20API-Places%20v1-4285F4.svg?style=flat-square)](https://developers.google.com/maps/documentation/places/web-service/op-overview)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)

An enterprise-grade asynchronous data ingestion pipeline built in n8n that completely automates localized B2B lead discovery. The system processes active marketing definitions from a centralized database, queries live data from the Google Places API with robust pagination handling, executes advanced string normalization inside a custom JavaScript deduplication core, and streams operational state summaries to Telegram metrics channels.

---

## ─── SYSTEM ARCHITECTURE VISUALIZATION ───


```

[Schedule Trigger] (Every morning at 08:00 AM)
│
▼
[Read Campaigns (Google Sheets)] ──► Checks for active marketing targets
│
▼
[Filter Active] ──► Discards inactive campaigns
│
▼
[Build Search Query] ──► Compiles "Business Type + City + Country"
│
▼
[Search Places API] ──► Fires HTTP POST payload to Google's v1 Text Search
│
▼
[Has Places?] ────(No Places Found)────► [Telegram Warning Bot]
│ (Yes)
├──► [Split Leads] (Normalizes paginated response array into items)
└──► [Get Existing Leads] (Pulls database records for cross-referencing)
│
▼
[Merge] ──► Connects parallel processing data streams
│
▼
[Duplicate Filter & Format (JavaScript Engine)] ──► String normalization & field mapping
│
▼
[Has New Leads?] ────(All Duplicates)────► [Telegram Notification: 0 Saved]
│ (Yes)
▼
[Save Raw Leads (Google Sheets)] ──► Appends data row-by-row to DB
│
▼
[Telegram Success Bot] ──► Broadcasts metrics (Collected, Saved, Skipped)

```

---

## ─── CORE SYSTEM FEATURES ───

* 🕒 **Automated Scheduling Core:** Operates autonomously via a localized cron schema to query listings without requiring human interaction.
* 📋 **Dynamic Campaign-Driven Orchestration:** Queries target records from a master document spreadsheet, allowing operational parameters to be modified without altering code.
* 🔌 **Enterprise API Optimization:** Communicates with the Google Places API v1 using structured POST requests. Employs advanced field masks (`X-Goog-FieldMask`) to minimize payloads and lower cloud costs.
* 📑 **Robust Pagination Management:** Tracks cursor pagination tokens (`nextPageToken`) over multiple iterative calls to ensure thorough lead extraction while respecting API rate limits.
* 🧠 **JavaScript Deduplication Core:** Uses custom ES6 logic to standardize and cross-reference records. This process runs character cleanups and normalizations on URLs to ensure data integrity.
* 🚨 **Asynchronous DevOps Operations Monitoring:** Implements parallel alert networks using a custom Telegram framework. It delivers status updates, empty query flags, and error stack details to off-site administrators.

---

## ─── TECH STACK INFRASTRUCTURE ───

* **Automation Engine:** n8n (Self-hosted / Cloud)
* **Data Integration Base:** Google Sheets Workspace API
* **Primary Query Layer:** Google Places API v1 (Text Search Integration Matrix)
* **Processing Core:** Node.js / JavaScript (ES6 Engine Run Context)
* **Operational Telemetry:** Telegram Bot API Infrastructure
* **Structured Language Formats:** JSON, REST API Webhook Architectures

---

## ─── DETAILED AUTOMATION LOGIC CORNERSTONES ───

### 1. The Ingestion Engine
The pipeline starts at `08:00 AM` every morning, calling the campaign configuration matrix to fetch the current target market targets. It passes records through a data filter, dropping items where status flags are not explicitly set to `Active`. This step outputs a uniform search parameters array:
```json
{
  "query": "Dentist Miami United States",
  "CampaignName": "Dentist in Miami",
  "DailyLimit": 50
}

```

### 2. The Pagination & Advanced API Pipeline

The system queries the Google Places API via an optimized HTTP POST request, passing fields into the payload structure while maintaining a strict API field mask. To handle datasets with more than 20 entries, the engine watches for the `nextPageToken` array parameter, looping back to capture additional records up to a predefined technical threshold.

### 3. JavaScript Custom Processing & Deduplication Logic

Once raw data payloads return, the workflow pulls the historical database and passes both arrays into a unified JavaScript code execution node. This setup isolates processing loads from the main database engine.

```javascript
// Data normalization and deduplication logic snippet
const newLeads = $('Split Leads').all();
let existingLeads = [];

try {
  existingLeads = $('Get Existing Leads').all().filter(item => item && item.json);
} catch (e) {
  console.log("Database initialized without records.");
}

const existingWebsites = new Set();
const existingMapUrls = new Set();

existingLeads.forEach(lead => {
  const website = lead.json['Website'] || '';
  const mapUrl = lead.json['Google Maps URL'] || '';
  if (website) existingWebsites.add(website.toString().toLowerCase().trim().replace(/\/$/, ''));
  if (mapUrl) existingMapUrls.add(mapUrl.toString().toLowerCase().trim().replace(/\/$/, ''));
});

```

---

## ─── DEPLOYMENT & OPERATION ENVIRONMENT MANUAL ───

### 1. Prerequisites Setup

* An operational **n8n environment** instance (Self-hosted Docker, Desktop desktop build, or n8n Cloud subscription).
* A **Google Cloud Platform (GCP) Developer Account** with the `Places API` resource module enabled.
* A target spreadsheet set up in **Google Sheets Workspace**.
* A **Telegram Bot API token** generated using `@BotFather`.

### 2. Environment Variables Configuration Matrix

Create a local `.env` configuration file or add the following parameters inside your secure cloud management panel:

```env
N8N_ENCRYPTION_KEY="your_secure_encryption_key_here"
GOOGLE_PLACES_API_KEY="AIzaSyA_ExampleKeyHere..."
TELEGRAM_BOT_TOKEN="1234567890:ABCdefGhIJKlmNoPQRsTUVwxyZ..."
TELEGRAM_CHAT_ID="5402528372"

```

### 3. Google Sheets Template Configuration

Create a spreadsheet inside Google Drive with the following columns to ensure the system functions correctly:

#### Tab Name: `Campaigns`

| Business Type | City | Country | Daily Lead Limit | Status |
| --- | --- | --- | --- | --- |
| Real Estate | Boston | United States | 50 | Active |
| Gym | Austin | United States | 30 | Inactive |

#### Tab Name: `Raw Leads`

Ensure the columns match these exact field headers: `Date`, `Business Name`, `Category`, `Address`, `City`, `Country`, `Phone`, `Website`, `Rating`, `Reviews`, `Google Maps URL`, `Status`.

### 4. Step-by-Step Workflow Import Guide

1. Download the production json definition file located under `workflows/lead_collector_workflow.json`.
2. Open your n8n workspace, select **Add Workflow**, and click the top-right menu icon to choose **Import from File**.
3. Select the target `.json` file from your device to load the entire processing framework.
4. Open the `Read Campaigns` and `Save Raw Leads` nodes to authenticate with your Google Workspace profile via OAuth2.
5. Click on the `Search Places API` node, select your Header Auth credentials, and configure an auth property key named `X-Goog-Api-Key` with your Google Cloud platform key as the value.
6. Open the Telegram node modules and input your custom `chatId` and token details. Save and toggle the workflow activation switch to `Active`.

---

## ─── ANALYTICS OUTLOOK & TELEMETRY SAMPLES ───

When the processing run finishes, the telemetry engine delivers formatted Markdown operational updates to your admin dashboard:

### Production Run Success Event

```markdown
📊 Lead Collection Summary

Campaign: Dentist in Miami
Collected: 60
Saved: 42
Duplicates Skipped: 18

```

### Zero-Yield Process Event

```markdown
ℹ️ Lead Collection Notice

Campaign: Dentist in Miami
No new leads saved. All 60 collected leads already exist in database.

```

### Operational Framework Failure

```markdown
❌ Lead Collector Workflow Error

Workflow: Workflow 1: Google Maps Lead Collector
Node: Search Places API
Message: 403 - Forbidden: Invalid FieldMask parameter or expired token credentials.

```

---

## ─── PRODUCTION GAINS & LEARNING SUMMARY ───

### Learning Takeaways

* Designed complex, cross-functional data automation workflows within the n8n application framework.
* Configured optimized integrations using Google Cloud Platform HTTP REST endpoints alongside structural payload filtering.
* Built memory-efficient data validation systems using JavaScript ES6 structures like `Set` arrays.
* Developed resilient data flows by implementing conditional branches and asynchronous exception alerts.

### Planned System Enhancements

* [ ] Integrate an AI evaluation node to analyze business websites and score prospective leads dynamically.
* [ ] Add a visual dashboard web interface to display scraped analytics metrics using charts.
* [ ] Build a secondary outbound step to trigger email sequences automatically when new records are written to the database.

---

Developed by Anisul Islam Kiron

```

```

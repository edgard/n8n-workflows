# n8n Workflows

A collection of n8n automation workflows for various use cases.

## Workflows

### Home Notification Enricher
**File:** `Home Notification Enricher.json`
**Documentation:** [Home Notification Enricher.README.md](./Home%20Notification%20Enricher.README.md)

AI-powered webhook endpoint that enriches alerts and notifications from home infrastructure, then sends human-friendly messages to Telegram.

**Features:**
- Single webhook endpoint for all alert sources (Gatus, Home Assistant, *arr stack)
- AI enrichment using MetaMCP tools to investigate alerts in real-time
- Smart notification sounds (alerts play sound, notifications are silent)
- Graceful fallback if AI fails

**Requirements:** n8n 2.x, OpenAI API (or compatible), MetaMCP server, Telegram Bot

---

### Paperless-ngx AI Document Classifier
**File:** `Paperless AI Document Classifier.json`
**Documentation:** [Paperless AI Document Classifier.README.md](./Paperless%20AI%20Document%20Classifier.README.md)

Automatically classifies documents in Paperless-ngx using AI. Analyzes document content and extracts metadata including tags, correspondents, document types, titles, and dates.

**Features:**
- Automatic document classification using OpenAI model
- Multilingual support (English, Portuguese, Polish)
- Smart tag matching from pre-created tags
- Auto-creates correspondents and document types
- Efficient one-at-a-time processing
- Handles pagination for large datasets

**Requirements:** n8n 2.2.1+, Paperless-ngx, OpenAI API

---

## General Setup

### Prerequisites
- n8n instance (self-hosted or cloud)
- Access to required services (varies by workflow)

### Installation
1. Download the workflow JSON file
2. In n8n, go to **Workflows → Import from File**
3. Select the JSON file
4. Configure credentials and settings as described in the workflow-specific README
5. Test and activate

## Repository Structure

```
.
├── README.md                                          # This file
├── [Workflow Name].json                               # Workflow file
├── [Workflow Name].README.md                          # Workflow documentation
└── ...
```

## Contributing

Feel free to submit additional workflows or improvements via pull requests.

## License

See LICENSE file for details.

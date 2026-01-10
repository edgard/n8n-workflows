# n8n Workflows

A collection of n8n automation workflows for various use cases.

## Workflows

### Home Alert Hub
**File:** `Home Alert Hub.json`
**Documentation:** [Home Alert Hub.README.md](./Home%20Alert%20Hub.README.md)

Central AI-powered alert processing hub for home infrastructure. Receives alerts from various sources, enriches them with AI for human-friendly messaging, sends to Telegram, and announces critical alerts via voice on house speakers.

**Features:**
- Single webhook endpoint for all alert sources
- AI enrichment with structured output (title, summary, severity, emoji)
- Smart notification sounds (critical = sound, info = silent)
- Voice announcements for critical/safety alerts via TTS
- Graceful fallback if AI fails

**Requirements:** n8n 2.x, OpenAI API, Telegram Bot, Home Assistant with TTS integration

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

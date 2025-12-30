# n8n Workflows

A collection of n8n automation workflows for various use cases.

## Workflows

### Paperless-ngx AI Document Classifier
**File:** `Paperless AI Document Classifier.json`  
**Documentation:** [Paperless AI Document Classifier.README.md](./Paperless%20AI%20Document%20Classifier.README.md)

Automatically classifies documents in Paperless-ngx using AI. Analyzes document content and extracts metadata including tags, correspondents, document types, titles, and dates.

**Features:**
- Automatic document classification using Claude AI
- Multilingual support (English, Portuguese, Polish)
- Smart tag matching from pre-created tags
- Auto-creates correspondents and document types
- Efficient one-at-a-time processing
- Handles pagination for large datasets

**Requirements:** n8n 2.2.1+, Paperless-ngx, OpenRouter API

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

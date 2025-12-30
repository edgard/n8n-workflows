# Paperless-ngx AI Document Classifier

An n8n workflow that automatically classifies documents in Paperless-ngx using AI. The workflow analyzes document content and extracts metadata including tags, correspondents, document types, titles, and dates.

## Table of Contents

- [Quick Start](#quick-start)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation) (Step-by-step guide)
- [Customization](#customization)
- [How It Works](#how-it-works)
- [Troubleshooting](#troubleshooting)
- [Advanced Configuration](#advanced-configuration)

## Quick Start

**TL;DR - Get started in 5 minutes:**

1. **Prerequisites:**
   - Create tag `ai-processed` in Paperless-ngx
   - Create classification tags (invoice, receipt, contract, etc.)
   - Get Paperless API token
   - Get OpenRouter API key

2. **Import & Configure:**
   - Import workflow JSON into n8n
   - Add Paperless API credential (HTTP Header Auth with `Token YOUR_TOKEN`)
   - Add OpenRouter API credential
   - Set Paperless URL in Config node

3. **Assign Credentials:**
   - Assign Paperless API to 9 HTTP nodes
   - Assign OpenRouter API to OpenRouter Chat Model node

4. **Test & Activate:**
   - Click "Execute Workflow" to test
   - Toggle "Active" switch to enable hourly processing

**First time?** Read the full [Installation](#installation) guide below for detailed step-by-step instructions.

## Features

- **Automatic Classification**: Uses AI to analyze document content and extract structured metadata
- **Multilingual Support**: Handles documents in English, Portuguese (Brazil), and Polish
- **Smart Tag Matching**: Selects from your pre-created tags (tags must exist in Paperless)
- **Auto-Creation**: Automatically creates new correspondents and document types when needed
- **Efficient Processing**: Processes documents one at a time with optimized API calls
- **Error Handling**: Robust error handling with fallback logic for duplicate entries
- **Pagination Support**: Handles large datasets with automatic pagination

## Requirements

### Software Requirements

- **n8n**: Version 2.2.1 or higher (tested on 2.2.1)
- **Paperless-ngx**: Any recent version with API access
- **AI Provider**: OpenRouter account (or alternative AI provider)

### Paperless-ngx Setup

Before importing this workflow, you need to configure tags in Paperless-ngx:

#### Required Tag

1. Log into your Paperless-ngx instance
2. Navigate to **Settings → Tags**
3. Create a new tag named exactly: `ai-processed` (lowercase, with hyphen)
4. This tag marks documents that have been processed by the AI workflow

#### Classification Tags

**Important**: The AI can only assign tags that already exist in Paperless-ngx. You must create all the tags you want the AI to use before running the workflow.

**Example tags to create:**
- `invoice`
- `receipt`
- `contract`
- `personal`
- `tax`
- `medical`
- `utilities`
- `financial`
- `important`
- `warranty`

Create tags based on your document organization needs. The AI will select from these tags when classifying documents.

#### Correspondents and Document Types

**No pre-creation needed!** The workflow automatically creates new correspondents and document types when the AI identifies ones that don't exist yet. You don't need to set these up in advance.

## Installation

### Step 1: Import the Workflow

1. Download the `Paperless AI Document Classifier.json` file
2. In n8n, click **Workflows** in the sidebar
3. Click **Add Workflow** → **Import from File**
4. Select the downloaded JSON file
5. Click **Import**

### Step 2: Configure Paperless-ngx Credentials

You need to create credentials for authenticating with your Paperless-ngx API.

1. In n8n, go to **Credentials** (gear icon in the sidebar)
2. Click **Add Credential**
3. Select **HTTP Header Auth**
4. Configure the credential:
   - **Name**: `Paperless API` (or any name you prefer)
   - **Name**: `Authorization`
   - **Value**: `Token YOUR_PAPERLESS_API_TOKEN`
5. Click **Save**

**To get your Paperless API token:**
1. Log into Paperless-ngx
2. Navigate to **Settings → API Tokens**
3. Click **Create Token**
4. Copy the token value
5. Use it in the format: `Token abc123yourtokenhere`

### Step 3: Configure OpenRouter Credentials

You need an OpenRouter API key for the AI classification.

1. Sign up at [OpenRouter](https://openrouter.ai/) if you don't have an account
2. Generate an API key from your dashboard
3. In n8n, go to **Credentials**
4. Click **Add Credential**
5. Select **OpenRouter API**
6. Configure the credential:
   - **Name**: `OpenRouter API` (or any name you prefer)
   - **API Key**: Your OpenRouter API key
7. Click **Save**

**Cost Considerations:**
- Default model: `anthropic/claude-sonnet-4.5` (~$3 per 1M input tokens, ~$15 per 1M output tokens)
- For large document collections, start with a small batch to estimate costs
- See [OpenRouter Pricing](https://openrouter.ai/pricing) for current rates

### Step 4: Configure the Workflow

Open the workflow and configure the **Config** node:

1. Double-click the **Config** node
2. Update the `baseUrl` value to your Paperless-ngx URL:
   ```
   http://your-paperless-instance:8000
   ```
   Examples:
   - Local: `http://localhost:8000`
   - Docker: `http://paperless:8000`
   - Kubernetes: `http://paperless.namespace.svc.cluster.local:8000`
   - External: `https://paperless.yourdomain.com`
3. Click **Save**

### Step 5: Assign Credentials to Nodes

The workflow has multiple nodes that need credentials. Assign them as follows:

**Nodes requiring Paperless API credentials:**
1. **Get AI Tag** → Set to `Paperless API` credential
2. **Fetch Unprocessed Documents** → Set to `Paperless API` credential
3. **Fetch Document Details** → Set to `Paperless API` credential
4. **Fetch Tags** → Set to `Paperless API` credential
5. **Fetch Correspondents** → Set to `Paperless API` credential
6. **Fetch Document Types** → Set to `Paperless API` credential
7. **Create Correspondent** → Set to `Paperless API` credential
8. **Create Document Type** → Set to `Paperless API` credential
9. **Patch Document** → Set to `Paperless API` credential

**Node requiring OpenRouter API credential:**
1. **OpenRouter Chat Model** → Set to `OpenRouter API` credential

**Quick way to assign credentials:**
1. In the workflow editor, press `Ctrl+F` (or `Cmd+F` on Mac)
2. Search for "Select credential"
3. Go through each node and select the appropriate credential
4. Click **Save** on each node

### Step 6: Test the Workflow

Before activating the workflow, test it manually:

1. Click **Execute Workflow** in the top right
2. Watch the execution progress
3. Check for any errors in the workflow
4. Verify documents are being classified correctly in Paperless-ngx

**Troubleshooting:**
- If "AI Tag Found?" fails → Make sure you created the `ai-processed` tag exactly as specified
- If API calls fail → Verify your Paperless-ngx URL is correct and accessible from n8n
- If authentication fails → Double-check your API token format: `Token abc123...`

### Step 7: Activate the Workflow

Once testing is successful:

1. Toggle the **Active** switch at the top right of the workflow editor
2. The workflow will now run automatically every hour (default schedule)

**To change the schedule:**
1. Double-click the **Schedule Trigger** node
2. Modify the interval (default: every 1 hour)
3. Options: minutes, hours, days, weeks, months
4. Click **Save**

## Customization

### Changing the AI Model

The workflow uses OpenRouter's Claude Sonnet 4.5 by default, but you can use any model:

1. Double-click the **OpenRouter Chat Model** node
2. Change the **Model** field to your preferred model:
   - `anthropic/claude-3.5-sonnet` - Faster, cheaper Claude
   - `openai/gpt-4-turbo` - OpenAI GPT-4 Turbo
   - `google/gemini-pro-1.5` - Google Gemini
   - `meta-llama/llama-3.1-70b-instruct` - Open source option
3. Optionally adjust **Temperature** (default: 0.1 for consistency)
4. Click **Save**

See [OpenRouter Models](https://openrouter.ai/models) for all available options.

### Using a Different AI Provider

You can replace OpenRouter entirely with other AI providers:

**Option 1: Use n8n's Built-in OpenAI Node**
1. Delete the **OpenRouter Chat Model** node
2. Add an **OpenAI Chat Model** node
3. Connect it to the **Classify Document** AI Agent node (use the `ai_languageModel` connection)
4. Configure with your OpenAI API key

**Option 2: Use Local LLM (Ollama)**
1. Install Ollama and a suitable model (e.g., `llama3`)
2. Delete the **OpenRouter Chat Model** node
3. Add an **Ollama Chat Model** node
4. Configure the Ollama connection URL
5. Connect it to the **Classify Document** AI Agent node

**Option 3: Use Azure OpenAI**
1. Delete the **OpenRouter Chat Model** node
2. Add an **Azure OpenAI Chat Model** node
3. Configure with your Azure credentials
4. Connect it to the **Classify Document** AI Agent node

### Adjusting Classification Rules

The AI classification behavior is controlled by the system prompt in the **Classify Document** node:

1. Double-click the **Classify Document** node
2. Click the **Options** tab
3. Modify the **System Message** to adjust:
   - Language support
   - Tag selection criteria
   - Correspondent detection rules
   - Document type classifications
   - Title formatting preferences
4. Click **Save**

### Processing More Documents at Once

By default, the workflow processes documents one at a time. To increase throughput:

1. Double-click the **Loop Over Documents** node
2. Change **Batch Size** from `1` to a higher number (e.g., `5`)
3. Click **Save**

**Warning:** Higher batch sizes may:
- Increase API costs significantly
- Cause rate limiting issues
- Reduce accuracy if the AI gets confused with multiple documents

### Modifying the Schedule

To change when the workflow runs:

1. Double-click the **Schedule Trigger** node
2. Modify the schedule:
   - **Interval**: Set the frequency (e.g., every 30 minutes, 2 hours, 1 day)
   - **Field**: Choose the time unit (minutes, hours, days, etc.)
3. Or switch to **Cron Expression** for more complex schedules
4. Click **Save**

**Examples:**
- Every 30 minutes: Interval = 30 minutes
- Every 6 hours: Interval = 6 hours
- Daily at 2 AM: Use Cron: `0 2 * * *`
- Weekdays at 9 AM: Use Cron: `0 9 * * 1-5`

## How It Works

### Workflow Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                         SETUP PHASE                             │
│  (Runs once per execution)                                      │
└─────────────────────────────────────────────────────────────────┘

1. Schedule Trigger (hourly)
   ↓
2. Load Configuration (Paperless URL)
   ↓
3. Verify AI Tag Exists → STOP if missing
   ↓
4. Fetch Reference Data:
   ├─ Fetch Tags (paginated)
   ├─ Fetch Correspondents (paginated)
   └─ Fetch Document Types (paginated)
   ↓
5. Build Lookup Dictionaries (aggregates all reference data)
   ↓
6. Fetch Unprocessed Documents (without "ai-processed" tag)
   ↓
7. Split Out Documents (converts array to individual items)

┌─────────────────────────────────────────────────────────────────┐
│                    PROCESSING LOOP                              │
│  (Runs once per document)                                       │
└─────────────────────────────────────────────────────────────────┘

8. For each document:
   ├─ Fetch full document details
   ├─ Merge with dictionaries
   ├─ AI analyzes document content
   ├─ Plan metadata updates
   │
   ├─ IF new correspondent needed:
   │  ├─ Create correspondent in Paperless
   │  └─ Merge new correspondent ID
   │
   ├─ IF new document type needed:
   │  ├─ Create document type in Paperless
   │  └─ Merge new document type ID
   │
   ├─ Update document in Paperless
   └─ Loop back for next document

9. Workflow ends when all documents processed
```

### Classification Process

For each document, the AI:

1. **Analyzes the content** including:
   - Original filename
   - Document title
   - Full text content
   - Creation date

2. **Extracts metadata**:
   - **Tags**: Selects from your pre-created tags (multiple allowed) - *AI can only use tags that already exist in Paperless*
   - **Correspondent**: Identifies the document issuer/sender - *automatically creates new correspondents if not found*
   - **Document Type**: Classifies the document category - *automatically creates new document types if not found*
   - **Title**: Generates a concise, descriptive title (max 80 chars)
   - **Date**: Extracts the primary document date (issue date)

3. **Smart matching and auto-creation**:
   - **Tags**: Must already exist in Paperless - AI selects from the available list
   - **Correspondents**: Matches against existing (case-insensitive), creates new ones automatically if needed
   - **Document Types**: Matches against existing (case-insensitive), creates new ones automatically if needed
   - Handles duplicate name errors gracefully

4. **Updates Paperless**:
   - Applies all metadata to the document via API
   - Adds the "ai-processed" tag so it won't be processed again

## Data Flow

### Execution Paths

The workflow handles four different scenarios:

**Path 1: All metadata exists**
- Document classified using existing tags, correspondent, and document type
- No API calls to create new entries

**Path 2: New correspondent needed**
- Creates new correspondent in Paperless
- Uses the new correspondent ID in document update

**Path 3: New document type needed**
- Creates new document type in Paperless
- Uses the new document type ID in document update

**Path 4: Both new correspondent and document type needed**
- Creates correspondent first
- Creates document type second
- Uses both new IDs in document update

All paths properly preserve data through conditional branches.

## Performance Optimization

The workflow is optimized for efficiency:

1. **Dictionary Building**: Tags, correspondents, and document types are fetched once before the loop starts, not for every document
2. **Pagination**: All API calls support pagination to handle large datasets
3. **Batch Processing**: Documents are processed one at a time by default (configurable)
4. **Error Handling**: Continues processing even if individual documents fail
5. **Rate Limiting**: Sequential processing prevents API rate limiting

**Expected Performance:**
- ~10-30 seconds per document (depends on document length and AI model)
- ~120-360 documents per hour with default settings
- API costs: $0.01-0.10 per document (varies by model and document length)

## Troubleshooting

### Common Issues

**"Required tag 'ai-processed' not found"**
- **Solution**: Create a tag named exactly `ai-processed` (lowercase with hyphen) in Paperless-ngx

**"Authentication failed" or 401 errors**
- **Solution**: Verify your Paperless API token format is `Token abc123...` (with "Token " prefix)

**"Connection refused" or timeout errors**
- **Solution**: Check that the Paperless-ngx URL in the Config node is correct and accessible from n8n

**AI classification is inaccurate**
- **Solution**: Adjust the system prompt in the Classify Document node to provide better instructions
- **Solution**: Try a more capable AI model (e.g., GPT-4 instead of GPT-3.5)

**Workflow processes the same documents repeatedly**
- **Solution**: Verify the "ai-processed" tag is being applied by checking document updates in Paperless
- **Solution**: Check the Patch Document node for errors

**High API costs**
- **Solution**: Switch to a cheaper model (see OpenRouter pricing)
- **Solution**: Use a local LLM with Ollama (free but requires self-hosting)
- **Solution**: Reduce batch size or processing frequency

### Viewing Execution Logs

To debug issues:

1. Click **Executions** in the n8n sidebar
2. Find the failed execution
3. Click to view details
4. Check each node for error messages
5. Look at the input/output data for each node

### Testing Individual Nodes

To test a specific node:

1. Click the node in the workflow editor
2. Click **Test Node** in the node panel
3. Review the output
4. Check for errors or unexpected data

## Maintenance

### Regular Checks

Periodically review:

1. **Execution history** for failed runs
2. **API costs** in your OpenRouter dashboard
3. **Classification accuracy** by sampling processed documents
4. **New correspondents/document types** created by the workflow

### Updating the Workflow

If you modify the workflow:

1. Make changes in n8n
2. Test thoroughly with sample documents
3. Export the updated workflow: **Workflow Settings → Export**
4. Save the new version

### Backup

Regular backups recommended:

1. Export the workflow JSON periodically
2. Store in version control (Git)
3. Document any customizations you make

## Advanced Configuration

### Custom Tag Rules

To modify tag selection logic, edit the system prompt in the **Classify Document** node. Example additions:

```
ADDITIONAL TAG RULES:
- Always add "invoice" tag if the document contains an invoice number
- Add "urgent" tag if the document has "urgent" or "ASAP" in the content
- Add "contract" tag for documents containing "hereby agree" or "contract terms"
```

### Multiple AI Models

For improved accuracy, you can chain multiple AI models:

1. Use a fast, cheap model for initial classification
2. Use a more capable model only for uncertain cases
3. Add an IF node to check confidence scores

### Notification on Failure

Add error notifications:

1. Add a new branch after the workflow
2. Connect to a Slack, Email, or Discord node
3. Send notification when errors occur

### Integration with Other Systems

The workflow can be extended to:

- Send notifications to Slack/Discord when documents are processed
- Upload processed documents to Google Drive or Dropbox
- Trigger webhooks to notify other systems
- Generate reports of processed documents

## Security Considerations

1. **API Tokens**: Store Paperless API tokens securely in n8n credentials
2. **AI Provider**: Document content is sent to the AI provider (OpenRouter/OpenAI) - review their privacy policy
3. **Network Access**: Ensure n8n can access Paperless-ngx (firewall rules, network policies)
4. **Sensitive Documents**: Consider using a local LLM for highly sensitive documents

## Support & Contributions

For issues, questions, or contributions:

1. Check the troubleshooting section above
2. Review n8n documentation: https://docs.n8n.io
3. Check Paperless-ngx API docs: https://docs.paperless-ngx.com/api
4. Open an issue in the repository (if applicable)

## License

This workflow is provided as-is. Modify and use freely for your needs.

## Credits

Built for n8n 2.2.1 and Paperless-ngx.

**Technologies used:**
- n8n (workflow automation)
- Paperless-ngx (document management)
- OpenRouter (AI model access)
- Claude Sonnet 4.5 (AI model)

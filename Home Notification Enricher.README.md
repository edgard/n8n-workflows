# Home Notification Enricher

AI-powered webhook endpoint that enriches alerts and notifications from home infrastructure, then sends human-friendly messages to Telegram.

## Features

- **Single webhook endpoint** for all alert sources (Gatus, Home Assistant, *arr stack, etc.)
- **AI enrichment** using MetaMCP tools to investigate alerts in real-time
- **Smart notification sounds** - Alerts play sound, notifications are silent
- **Deduplication** - Ignores duplicate messages within 2-minute window
- **Human-friendly output** - Messages your family can understand
- **Graceful fallback** - Formats raw payload if AI fails

## Supported Sources

| Source | Detection | AI Investigation |
|--------|-----------|------------------|
| **Gatus** | `endpoint_name`, `condition_results` | Checks endpoint status, K8s pod logs |
| **Home Assistant** | `event_type`, `entity_id` | Checks entity states, sensor history |
| **Radarr/Sonarr** | `eventType`, `instanceName` | None needed (notifications) |
| **AlertManager** | `alertname` | Checks related K8s resources |

## Setup

### Prerequisites

1. **n8n** instance (v2.x)
2. **LiteLLM** proxy with OpenAI-compatible API
3. **MetaMCP** server with infrastructure tools configured
4. **Telegram Bot** with chat ID

### Credentials Required

Create these credentials in n8n before importing:

| Name | Type | Purpose |
|------|------|---------|
| `LiteLLM API` | OpenAI API | AI model access |
| `MetaMCP API` | HTTP Bearer Auth | MCP tools endpoint |
| `Telegram Bot` | Telegram API | Message delivery |

### Configuration

After importing the workflow, update these values:

1. **OpenAI Chat Model** node:
   - Update model name if not using `github_copilot/gpt-5-mini`
   - Connect to your LiteLLM credential

2. **MetaMCP** node:
   - Update `endpointUrl` to your MetaMCP server
   - Connect to your bearer auth credential

3. **Send Telegram** node:
   - Replace `YOUR_TELEGRAM_CHAT_ID` with your actual chat ID
   - Connect to your Telegram Bot credential

## Webhook Endpoint

```
POST /webhook/alert-enricher
```

### Example Payloads

**Gatus Alert:**
```json
{
  "endpoint_name": "plex",
  "endpoint_url": "https://plex.example.com",
  "condition_results": [
    {"condition": "[STATUS] == 200", "success": false}
  ]
}
```

**Home Assistant Event:**
```json
{
  "event_type": "state_changed",
  "entity_id": "binary_sensor.front_door",
  "old_state": "off",
  "new_state": "on"
}
```

**Sonarr Download:**
```json
{
  "eventType": "Download",
  "instanceName": "Sonarr",
  "series": {"title": "Breaking Bad"},
  "episodes": [{"seasonNumber": 1, "episodeNumber": 1, "title": "Pilot"}]
}
```

## Message Format

Messages are sent in this format:

```
🎬 Sonarr: Breaking Bad downloaded

Season 1 Episode 1 "Pilot" is ready to watch.
```

For alerts, the AI investigates first:

```
🔴 Gatus: Plex down

Plex failed its health check. The pod is running but showing connection refused errors in the logs.
```

### Notification Sounds

The workflow distinguishes between **alerts** and **notifications**:

| Type | Sound | Examples |
|------|-------|----------|
| **Alert** | 🔔 Plays sound | Gatus health failures, Home Assistant sensor alerts |
| **Notification** | 🔕 Silent | Radarr/Sonarr downloads, informational messages |

This lets you stay informed about downloads without being disturbed, while still getting audible alerts for things that need attention.

## Architecture

```
Webhook → Respond 202 → Dedup Check → Is Duplicate?
                                          │
                      ┌───────────────────┘
                      ↓ (not duplicate)
              Prepare AI Context → AI Agent → AI Success?
                                      │           │
                                      │      ┌────┴────┐
                         ┌────────────┘      ↓         ↓
                         │           Format HTML   Fallback
                    Sub-nodes:           │         Format
                    - OpenAI Model       └────┬────┘
                    - MetaMCP                 ↓
                    - Output Parser    Send Telegram
```

## Customization

### Adjusting Deduplication Window

In the **Dedup Check** node, modify:
```javascript
const DEDUP_WINDOW_MS = 2 * 60 * 1000; // Change 2 to desired minutes
```

### Modifying AI Behavior

Edit the system message in the **AI Agent** node to:
- Add new source detection rules
- Change investigation requirements
- Adjust output format/style

### Adding New Sources

1. Add detection rules to the AI system prompt
2. Optionally add fallback detection in the **Fallback Format** node

## License

MIT

# Home Alert Hub

A central n8n workflow that processes alerts from home infrastructure, enriches them with AI for human-friendly messaging, sends notifications to Telegram, and announces critical alerts via voice on house speakers.

## Quick Start

1. **Prerequisites:**
   - n8n instance with OpenAI API credentials
   - Telegram Bot token and chat ID
   - Home Assistant with TTS integration (for voice announcements)

2. **Import & Configure:**
   - Import `Home Alert Hub.json` into n8n
   - Add OpenAI API credential
   - Add Telegram Bot credential
   - Add Home Assistant API credential
   - Update Telegram chat ID in "Send Telegram" node

3. **Test:**
   - Activate the workflow
   - Send a test POST to `https://your-n8n/webhook/alert-enricher`

## Features

- **Single Webhook Endpoint**: One URL for all alert sources
- **AI Enrichment**: Transforms technical alerts into human-friendly messages
  - Generates concise titles (2-4 words)
  - Creates readable summaries
  - Assigns severity (critical/warning/info)
  - Picks appropriate emoji
  - Determines if voice announcement is needed
- **Telegram Notifications**: HTML-formatted messages with smart sound behavior
- **Voice Announcements**: Critical alerts spoken on house speakers via TTS
- **Graceful Fallback**: If AI fails, raw alert is still sent to Telegram

### Notification Sound Behavior

| Severity | Sound |
|----------|-------|
| Critical | Always |
| Warning (alert) | Yes |
| Warning (notification) | Silent |
| Info | Silent |

## Requirements

- **n8n**: Version 2.x or higher
- **OpenAI API**: For AI enrichment
- **Telegram Bot**: For notifications
- **Home Assistant**: With TTS integration (for voice announcements)

## Installation

### Step 1: Import the Workflow

1. Download `Home Alert Hub.json`
2. In n8n: **Workflows > Import from File**
3. Select the JSON file

### Step 2: Configure Credentials

**OpenAI API:**
1. Go to **Credentials > Add Credential > OpenAI API**
2. Enter your API key

**Telegram Bot:**
1. Create a bot via @BotFather on Telegram
2. Go to **Credentials > Add Credential > Telegram API**
3. Enter the bot token

**Home Assistant API:**
1. Generate a long-lived access token in HA (Profile > Security)
2. Go to **Credentials > Add Credential > Home Assistant API**
3. Enter host and access token

### Step 3: Assign Credentials

Assign credentials to these nodes:
- **OpenAI Chat Model**: OpenAI API
- **Send Telegram**: Telegram API
- **Call Home Assistant TTS**: Home Assistant API

### Step 4: Configure Telegram Chat ID

1. Open the **Send Telegram** node
2. Update `chatId` to your Telegram chat/user ID

### Step 5: Activate

Toggle the workflow to **Active**.

## Webhook Endpoint

```
POST https://your-n8n-instance/webhook/alert-enricher
Content-Type: application/json
```

### Payload Format

```json
{
  "message": "Human-readable description of what happened",
  "data": {
    "event": "category_name",
    "category": "specific_type",
    "device": "Device Name",
    "state": "current_state"
  }
}
```

### Event Types

| Event | Description | Voice |
|-------|-------------|-------|
| `safety` | Water leaks, smoke, CO | Yes |
| `security` | Door/window intrusion, alarms | Yes |
| `system` | Infrastructure issues | Maybe |
| `maintenance` | Low battery, filter replacement | No |
| `environment` | Air quality, humidity | No |
| `power_management` | Device auto-off, charging | No |

## Workflow Structure

```
Webhook
  ↓
Respond 202 (async)
  ↓
Message Processor (AI)
  ↓
AI Success? ─── No ──→ Fallback Format → Send Telegram
  │
 Yes
  ↓
Format HTML Message
  ↓
├─→ Send Telegram
└─→ IF Voice? ─── Yes ──→ Call Home Assistant TTS
```

### AI Output

The AI analyzes the incoming payload and extracts:

| Field | Description |
|-------|-------------|
| `message_type` | `alert` (action needed) or `notification` (info only) |
| `title` | 2-4 word punchy title |
| `severity` | `critical`, `warning`, or `info` |
| `emoji` | Single relevant emoji |
| `summary` | 1-3 sentence human-friendly summary |
| `source` | System name (e.g., "Home Assistant") |
| `voice` | Boolean - should this be announced? |

### Example Output

**Telegram:**
```
🚰 Water Leak Detected 🔴

Water has been detected at the bathroom sink sensor.
Please check immediately to prevent damage.
```

**Voice (summary only):**
```
"Water has been detected at the bathroom sink sensor.
Please check immediately to prevent damage."
```

## Customization

### Change AI Model

Edit the **OpenAI Chat Model** node:
- Model: `gpt-4o-mini`, `gpt-4o`, etc.
- Temperature: Lower = more consistent, higher = more creative

### Modify AI Behavior

Edit the **Message Processor** node system prompt to adjust:
- Severity classification rules
- Voice decision criteria
- Summary style and length
- Title formatting

### Add More Notification Channels

Add nodes after **Format HTML Message** for:
- Slack notifications
- Email alerts
- Discord webhooks
- Push notifications

## Troubleshooting

### No Telegram Message

1. Check workflow execution logs in n8n
2. Verify Telegram credentials
3. Confirm chat ID is correct

### No Voice Announcement

1. Verify Home Assistant credential
2. Confirm TTS integration is configured in HA
3. Check media player entity exists
4. Check HA logs for TTS errors

### AI Enrichment Fails

1. Check OpenAI API key is valid
2. Verify API quota not exceeded
3. Fallback should still send raw message

## License

See LICENSE file for details.

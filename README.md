# WhatsApp Manager — Evolution API UI

A browser-based WhatsApp management interface built on top of [Evolution API v2](https://github.com/EvolutionAPI/evolution-api). No installation, no Node.js, no build step — just open `index.html`.

**Current version: [v1.1.0](CHANGELOG.md)**

---

## Prerequisites

- A running **Evolution API v2** instance (self-hosted)
- A connected WhatsApp instance on that server
- A modern browser (Chrome / Edge / Firefox)

---

## Setup

1. Download or clone this repo
2. Open `index.html` directly in your browser
3. Click the **Settings** gear button in the top-right
4. Fill in:
   - **Business / App Name** — display name shown in the header (e.g. your company name)
   - **Server URL** — your Evolution API base URL (e.g. `https://your-server.com`)
   - **Instance Name** — the WhatsApp instance name configured in your Evolution API
   - **API Key** — your Evolution API key
5. Click **Save** — credentials are stored locally in your browser (never sent anywhere else)

---

## Features

### Chat Inbox
A full WhatsApp-style chat inbox — browse all conversations, send and receive messages in real time.

| Feature | Description |
|---------|-------------|
| Conversation list | All chats with avatar, name, last message preview, unread badge, and timestamp |
| Real-time polling | New messages appear automatically (3 s message poll, 30 s conversation poll) |
| Message bubbles | Text, image, video, audio, document, sticker, location, contact, poll, reactions |
| Read receipts | Double blue ticks (✓✓) show when a message has been read; single grey ticks for sent/delivered |
| RTL support | Hebrew, Arabic, and other RTL text renders correctly; English stays LTR — per message, automatically |
| Reply | Quote any message in your reply |
| React | Send emoji reactions to any message |
| Delete message | Remove messages with WhatsApp-style confirmation — **Delete for everyone** (your sent messages, within ~60 h) or **Delete for me** (any message, local only) |
| Select mode | Long-press / right-click any message to enter multi-select — copy, forward, or delete a batch |
| Drag-to-reorder | Reorder queued messages by dragging |
| Scroll pagination | Scroll up to load older message history |
| Persistent unread badges | Unread counts persist across page refreshes |

### Messaging
| Type | Description |
|------|-------------|
| Text | Send text messages to one or multiple recipients (chip-based input) |
| Media | Send images, videos, documents with optional caption |
| Voice Note | Send audio files as voice messages |
| Location | Send GPS coordinates with place name and address |
| Contact | Share a contact card (vCard) |
| Reaction | React to a specific message with an emoji |
| Poll | Create a poll with up to 12 options |
| Buttons | Send interactive button messages |
| List | Send interactive list messages with sections |
| Status/Story | Post WhatsApp status updates (text, image, video, audio) |

### Groups
- **Browse & Send** — fetch all groups, select one or more, broadcast a message
- **Create Group** — create a new group with participants
- **Manage Group** — update subject, description, picture, participants, settings, invite links, ephemeral timer

### Bulk Sending
- Send to multiple recipients or groups with a **randomized delay** between messages (configurable min/max seconds) to simulate human sending behavior

### Scheduled Sending
Schedule any Compose or Group Broadcast job to send automatically at a future time. The browser tab must remain open.

| Action | Description |
|--------|-------------|
| **Schedule** | Pick a future date/time — job queued and sent automatically |
| **Cancel & Edit** | Cancels the pending job and loads it back into the tab for full editing |
| **Duplicate** | Loads job content into the tab as a new draft — original untouched |
| **Resend** | Clone a completed or cancelled job with a new send time |
| **Restore** | Re-activate a cancelled job (only if scheduled time hasn't passed) |
| **Cancel** | Cancel a pending job without editing |

- Jobs are stored in **IndexedDB** (survives page refresh, handles large payloads)
- Scheduler polls every 30 seconds; jobs execute in order with the configured random delay between messages

### Phone Number Handling
- Validates phone numbers using [libphonenumber-js](https://github.com/nicolo-ribaudo/libphonenumber-js)
- Auto-normalizes local numbers to E.164 format (e.g. `0501234567` → `972501234567` for IL)
- Configure default country in Settings for local number parsing

---

## Server Deployment — `config.json`

When hosting on a web server, you can pre-configure all settings centrally using a `config.json` file placed next to `index.html`. Users won't need to touch Settings — everything is loaded automatically.

Copy `config.example.json` → `config.json` and fill in your values:

```json
{
  "appName":  "My Business",
  "base":     "https://your-evolution-api-server.com",
  "instance": "MyInstance",
  "apikey":   "your-api-key-here",
  "country":  "IL",
  "delayMin": 1,
  "delayMax": 5
}
```

Any field can be omitted — omitted fields fall back to the user's local browser settings.

> **Note:** `config.json` is listed in `.gitignore` so your credentials are never committed to the repo.

---

## Security

- Credentials are stored in your **browser's localStorage** (local use) or in `config.json` on your server (hosted use)
- Nothing is hardcoded in the source — the repo contains zero credentials
- Credentials never leave your browser except in direct API calls to your own Evolution API server

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for full version history.

---

## License

MIT — free to use and modify.

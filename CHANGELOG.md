# Changelog

All notable changes to this project are documented here.

---

## [1.2.0] — 2026-04-10

### Improvements

#### Scheduler — Background Tab Support
- Scheduled messages now fire reliably even when the tab is in the background (not the active/focused tab)
- Replaced the main-thread `setInterval` poller with a **Web Worker**-based tick; browsers throttle main-thread timers in hidden tabs but do not throttle Worker timers
- Falls back to plain `setInterval` in environments that do not support Web Workers
- Updated all UI copy from "The browser tab must remain open" to clarify the tab only needs to be open — it does not need to be the active tab

---

## [1.1.0] — 2026-04-10

### New Features

#### Delete Message
- WhatsApp-style delete confirmation modal with three options:
  - **Delete for everyone** — calls `DELETE /message/delete/`; only available when every selected message was sent by you (`fromMe`); works within ~60 hours of sending
  - **Delete for me** — calls `DELETE /message/deleteMessage/` (best-effort); always removes the message from your local view regardless of API result
  - **Cancel** — dismisses without action
- Available from the **right-click context menu** (single message) and the **select-mode toolbar** (multi-message batch delete)
- Toolbar delete button is disabled when no messages are selected
- Modal auto-dismissed when switching or closing a conversation
- Escape key closes the modal
- Toast notification on partial failure (e.g. message too old to delete for everyone)

#### RTL / LTR Text Direction
- All message text elements now carry `dir="auto"` — Hebrew/Arabic renders right-to-left, English/Latin stays left-to-right, automatically per bubble
- Applied to: plain text, image/video/document captions, contact messages, poll title and options, quoted message previews, chat list contact names and last-message previews

### Bug Fixes

#### Security
- XSS — avatar `onerror` attribute no longer uses `innerHTML` with unescaped initials; switched to sibling-element show/hide pattern
- XSS — `quotedMsg.id` is now sanitised (`/[^a-zA-Z0-9_-]/g`) before being embedded in `onclick`
- XSS — image lightbox `onclick` now reads URL from `data-src` attribute instead of embedding the raw URL in the inline handler string
- XSS — group name, subject, and description now escaped with `chatEsc()` in all render paths

#### Crashes / Runtime Errors
- `chatState.allFetchedMessages` was not initialised in the state object — accessing it before the first conversation opened threw `TypeError`
- `case 'location'` crashed when `m.text` was `null` or `undefined`
- Location coordinates were not passed through `encodeURIComponent` before being placed in a Google Maps URL
- `FileReader` base64 result was indexed with `[1]` without checking that a comma was present in the data URL

#### API
- `chatApiCall` was silently dropping the request body on `DELETE` calls — the Evolution API delete endpoints require a JSON body, so deletes were failing

#### Lifecycle
- Delete modal was not dismissed when switching conversations — the wrong `remoteJid` (new conversation's JID) could be used if the user confirmed deletion after switching
- Delete modal was not dismissed when closing a conversation
- `chatCloseConversation` did not reset `selectMode`, `selectedMsgs`, or the toolbar visibility
- `chatConfirmDeleteForEveryone` and `chatConfirmDeleteForMe` now guard against `activeJid` being `null`

#### UI / State
- Select-mode toolbar delete button used a fragile `[onclick="..."]` CSS attribute selector; replaced with a stable `id`
- Delete button incorrectly started in an enabled state before `chatUpdateSelectCount` ran; now starts `disabled` in HTML
- `chatCopySelected` and `chatForwardSelected` used `Array.includes()` (O(n)) to filter selected messages; replaced with `Set.has()` (O(1))
- Quoted message text defaulted to `undefined` if `m.quotedMsg.text` was missing; now defaults to `''`

---

## [1.0.1] — 2026-04-07

### Bug Fixes
- Fixed scroll-up pagination to correctly load older messages from the API — scroll position now preserved when prepending older messages
- Ghost unread indicators on groups cleared optimistically before the API markRead call
- Fixed stale-JID race in `chatPollMessages`, `chatFetchOlderFromApi`, and `chatSendMediaMsg`
- Fixed double-send race with `_sending` lock and `finally`-block release
- Fixed `chatMarkRead` to use per-message `remoteJid` instead of the conversation JID
- Added `visibilitychange` listener for immediate refresh when the tab regains focus

---

## [1.0.0] — 2026-04-05

### New Features — Chat Inbox
Full WhatsApp-style chat inbox merged in from the `whatsapp-chat-feature` branch.

- Conversation list with avatar, name, last message preview, unread badge, and timestamp
- Real-time polling — new messages every 3 s, conversation list every 30 s
- Message bubbles for all types: text, image, video, audio, document, sticker, location, contact, poll, reactions
- Read receipts — double blue ticks via `MessageUpdate` status history array (SERVER_ACK → DELIVERY_ACK → READ → PLAYED)
- Reply — quote any message in your reply
- React — send emoji reactions
- Select mode — multi-select messages to copy or forward
- Drag-and-drop reordering of queued messages in Compose and Group Broadcast
- Persistent unread badges — survive page refresh via `localStorage`
- Group name resolution — `verifiedName` → `subject` → fallback; persisted to avoid re-fetching

---

## [0.1.3] — 2026-03-20

### New Features
- **Scheduled Sending** — schedule any Compose or Group Broadcast job to send at a future date/time
- New **Scheduled** sidebar tab with live pending-job badge
- Job cards show status, scheduled time, type, recipients, result, and a cancel button
- 30-second background poller executes jobs automatically while the tab is open
- API Response bar hidden on Scheduled and Profile Settings tabs (irrelevant there)

### Bug Fixes
- Removed stray `</div>` that was closing the scroll container prematurely
- Forced tab panel visibility with inline style to fix intermittent rendering glitch
- Fixed missing closing div before the response panel
- Reset scroll position to top when switching tabs

---

## [0.1.2] — 2026-03-15

### New Features
- **Group Broadcast Queue** — Groups › Browse & Send now uses the same multi-card message sequence builder as the Compose tab
- Groups can receive sequences of Text, Media, Voice, Poll, and Buttons messages in one broadcast
- Broadcast button shows live totals: "Broadcast 3 messages to 5 groups (15 sends)"
- Validation runs upfront before any sends begin

### Improvements
- Scheduled jobs: clicking a card body now toggles the details panel
- Scheduled jobs: uniform button widths
- Group list max-height increased to 60 vh

---

## [0.1.1] — 2026-03-10

### New Features
- **Message Queue Builder** in Compose tab — add multiple message cards of different types (Text, Media, Voice, Poll, Buttons) and send them as a sequence to all recipients
- Each card is independently configurable and removable
- Send button shows live totals and validates all cards before sending
- Default text card loaded on page open

### Improvements
- Accept numeric dial prefix in country code field (e.g. `972`, `44`) in addition to ISO codes
- Allow manual country code input in Settings

---

## [0.1.0] — 2026-03-05

### New Features
- **Compose Tab** — unified multi-type, multi-recipient sending panel
- Chip-based recipient input — type a number and press Enter/comma; bulk-paste supported
- Message types: Text, Media (image/video/document), Voice Note, Location, Contact, Reaction, Poll, Buttons, List, Status/Story
- Randomized delay between sends (configurable min/max seconds)
- Phone number validation and E.164 normalisation via libphonenumber-js

---

## [0.0.9] — 2026-02-28

### New Features
- **File Upload** — Media and Audio tabs now support "Choose file from computer" alongside URL input
- Files are read as base64 via `FileReader` and sent directly to the Evolution API
- Auto-fills MIME type and filename from the selected file
- Green badge shows the selected filename; cleared if a URL is typed instead

---

## [0.0.8] — 2026-02-20

### New Features
- **`config.json` support** — when hosted on a web server, pre-configure all settings centrally; users skip the Settings modal entirely
- `serverCfg` takes priority over `localStorage` for all fields
- Settings modal shows a notice when a server config is active
- `config.example.json` template added; `config.json` is gitignored

---

## [0.0.7] — 2026-02-15

### Improvements
- Configurable **Business / App Name** — removes all hardcoded "Studio Shimshi" references
- App name stored in `localStorage`; updates page title and header dynamically
- README added with setup instructions and feature overview

---

## [0.0.6] — 2026-02-10

### Initial Release
- Messaging: Text, Media, Audio, Location, Contact, Reaction, Poll, Buttons, List, Status/Story
- Groups: browse, broadcast, create, manage participants and settings
- Phone number validation via libphonenumber-js; E.164 normalisation
- Randomized delay between bulk sends
- `localStorage`-only config — no hardcoded credentials

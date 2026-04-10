# Changelog

All notable changes to this project are documented here.

---

## [1.1.0] — 2026-04-10

### New Features

#### Delete Message
- WhatsApp-style delete confirmation modal with three options:
  - **Delete for everyone** — calls Evolution API `DELETE /message/delete/`; only available when every selected message was sent by you (`fromMe`); works within ~60 hours of sending
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
- **XSS** — avatar `onerror` attribute no longer uses `innerHTML` with unescaped initials; switched to sibling-element show/hide pattern
- **XSS** — `quotedMsg.id` is now sanitised (`/[^a-zA-Z0-9_-]/g`) before being embedded in `onclick`
- **XSS** — image lightbox `onclick` now reads URL from `data-src` attribute instead of embedding the raw URL in the inline handler string
- **XSS** — group name, subject, and description now escaped with `chatEsc()` in all render paths
- Group description snippet in group manager now escaped

#### Crashes / Runtime Errors
- `chatState.allFetchedMessages` was not initialised in the state object — accessing it before the first conversation opened threw `TypeError`
- `case 'location'` crashed when `m.text` was `null` or `undefined`
- Location coordinates were not passed through `encodeURIComponent` before being placed in a Google Maps URL
- `FileReader` base64 result was indexed with `[1]` without checking that a comma was present in the data URL

#### API
- `chatApiCall` was silently dropping the request body on `DELETE` calls — the Evolution API delete endpoints require a JSON body, so deletes were failing with 400/422 errors

#### Lifecycle
- Delete modal was not dismissed when switching conversations — the wrong `remoteJid` (new conversation's JID) would be used if the user confirmed deletion after switching
- Delete modal was not dismissed when closing a conversation
- `chatCloseConversation` did not reset `selectMode`, `selectedMsgs`, or the toolbar visibility
- `chatConfirmDeleteForEveryone` and `chatConfirmDeleteForMe` now guard against `activeJid` being `null`

#### UI / State
- Select-mode toolbar delete button used a fragile `[onclick="..."]` CSS attribute selector to find itself; replaced with a stable `id`
- Delete button incorrectly started in an enabled state before `chatUpdateSelectCount` ran; now starts `disabled` in HTML
- `chatCopySelected` and `chatForwardSelected` used `Array.includes()` (O(n)) to filter selected messages; replaced with `Set.has()` (O(1))
- Quoted message text defaulted to `undefined` if `m.quotedMsg.text` was missing; now defaults to `''`

---

## [1.0.1] — 2026-04-07

### Bug Fixes
- Fixed scroll-up pagination to correctly load older messages from the API
- Ghost unread indicators no longer appear after marking chats as read

---

## [1.0.0] — 2026-04-05

### Initial Release
- Full chat inbox with conversation list and real-time polling
- Message types: text, image, video, audio, document, sticker, location, contact, poll, reactions
- Read receipts (double blue ticks) via `MessageUpdate` status history
- Select mode with copy and forward
- Drag-and-drop reordering of message queues
- Compose panel: text, media, voice note, location, contact, reaction, poll, buttons, list, status/story
- Group management: browse, create, manage participants and settings
- Bulk sending with randomized delay
- Scheduled sending with IndexedDB persistence
- Phone number validation and E.164 normalisation
- `config.json` support for server-hosted deployments

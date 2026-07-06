# plagComms

**plagComms** is a multi-platform live chat aggregator and OBS overlay tool for streamers. It pulls chat from Twitch, TikTok Live, and YouTube Live into a single unified overlay — and lets multiple streamers share each other's chat in real time through a room system.

> **Current Version:** 1.0.2  
> **Platform:** Windows (standalone `.exe`)

---

## What It Does

plagComms runs silently in your system tray and serves a browser-source overlay that you drop into OBS. It connects to your live streams across platforms, merges all chat into one feed, and broadcasts it to your overlay in real time. You can also join a shared room with other streamers so their audiences' chats appear in your overlay (and yours in theirs).

---

## Features

### Platform Support
- **Twitch** — chat, cheers (bits), subscriptions, gift subs, new followers, raids, channel point redemptions, Watch Streak Power-Ups, moderation actions, and ad-break notifications
- **TikTok Live** — chat, gifts (with real gift images), likes, follows, shares, subscriptions, superfan events
- **YouTube Live** — chat (with emoji & stickers rendered inline), Superchats, Super Stickers, memberships (new, milestones, gifted), and synthesized new-subscriber alerts

### OBS Overlay
- Browser-source overlay served locally over WebSocket
- Scrollable chat feed with smart auto-scroll (pauses when you scroll up)
- Per-message fade/lifetime control and max message count
- Configurable font size, message alignment (left/right), and border styling
- Platform-colored usernames with real Twitch badge images
- BTTV, FFZ, and 7TV emote support with hover tooltips
- Profile picture display with local disk cache

### OBS Browser Dock
- Full chat dock at `http://localhost:54473/chatbot/dock` — add as a Custom Browser Dock in OBS
- Send messages to Twitch and YouTube directly from the dock
- Mod action buttons (Delete, Timeout, Ban) always visible on Twitch messages
- Clickable links in chat messages
- Gift images (TikTok) rendered inline
- BTTV/FFZ/7TV emote images rendered inline

### Channel Stats Overlay
- Live viewer counts, follower counts, subscriber counts, and YouTube member counts at `http://localhost:54473/stats-overlay`
- Static counts (followers, subscribers, members) **persist between streams and across app restarts** — populated from cache the moment plagComms opens
- YouTube subscriber and member counts polled every 10 minutes even when not live
- Platform cards visible as long as credentials are configured — no need to be streaming

### BTTV Emote Tracking
- Channel Point rewards named "BTTV", "BetterTTV", or "Better TTV" are automatically detected
- On redemption — and on a 30-minute background timer — plagComms re-checks your BetterTTV channel emote set and posts mod-style messages for **both** added and removed emotes (with the emote image)
- Shows in the streamer chat, pop-out, and (optionally) the overlay; also pushed to the add-on WebSocket

### Ad Break Notifications (Twitch)
- A drop-down notice on your streamer chat and pop-out (never the on-stream overlay)
- Amber **"⏰ Ad in 0:60"** countdown ~1 minute before a scheduled ad, turning red **"🔴 AD BREAK · 0:30 left"** while one is running
- A **Skip** button snoozes (delays) the upcoming ad, plus a dismiss button
- Requires the `channel:read:ads` / `channel:manage:ads` permissions (re-connect Twitch to enable)

### Multi-Streamer Room System
- Create or join a password-protected room shared with other streamers
- Real-time WebSocket relay — each streamer's chat appears in everyone's overlay
- Public room listing so you can discover open rooms
- Room members shown with platform tags (TW / TT / YT)
- Relay hub deployed on Railway (stateless, no database)

### Native Pop-Out Chat Window
- Dockable native chat window separate from the overlay
- Replays the last 200 messages on open
- Send messages to Twitch and YouTube from the input bar
- Emote autocomplete (`:query` syntax for BTTV/FFZ/7TV)

### Settings & Security
- Secrets (OAuth tokens, API keys) stored in **Windows Credential Manager** via `keyring` — never written to disk in plain text, never sent to any plagComms server
- All other settings persisted to `%APPDATA%\plagComms\settings.json`
- All user data stored in `%APPDATA%\plagComms\` — nothing ever written next to the `.exe`
- Encrypted backup/restore — export all settings and credentials to a file, import on another PC
- All API calls go directly from your machine to platform APIs — plagComms has no backend

### Data Storage
All user-generated files live in `%APPDATA%\plagComms\`:

| File / Folder | Contents |
|---------------|----------|
| `settings.json` | App configuration |
| `chat.db` | SQLite chat history (opt-in) |
| `stats_cache.json` | Persistent follower/subscriber/member counts |
| `pfp_cache\` | Cached profile pictures |
| `tiktok_emotes\` | Custom TikTok emote images (drop PNG/GIF files here) |
| `debug\` | Debug log files (opt-in, off by default) |

### Chat Filtering
- Per-platform event toggles (hide subs, gifts, follows, etc. independently)
- Banned phrase list (word-boundary regex matching)
- Excluded user list

### Chat History Logging
- Opt-in SQLite logging of your own chat (never room/shared chat)
- Configurable retention: 7 days / 30 days / 90 days / 1 year / forever
- Built-in log viewer UI with search by username, keyword, platform, and date range
- CSV export

### Session Stats
- Per-session counters for messages, bits, subs, follows, redemptions, gifts, Superchats, and memberships across all platforms
- Clear stats button in settings

### Updates
- Checks for new versions on startup (non-blocking, background thread)
- Update banner shown in-app when a new version is available
- Full changelog viewer built into the app

### Overlay Appearance Customization
- Max messages displayed
- Message lifetime (seconds)
- Font size
- Right-align mode
- Optional border (color, width, radius)
- Per-platform accent colors (Twitch purple, TikTok red, YouTube red — all configurable)
- VIP Highlight — rainbow-cycling border for designated usernames

---

## Installation

1. Download the latest `plagComms.exe` from [Releases](https://github.com/plagrizr/plagcomms/releases/latest)
2. Run the exe — no installer needed
3. plagComms starts in the system tray
4. Open the settings window from the tray icon and connect your platforms
5. Add `http://localhost:54473/chatbot` as a Browser Source in OBS
6. Optionally add `http://localhost:54473/chatbot/dock` as a Custom Browser Dock in OBS

---

## Connecting Platforms

| Platform | What you need |
|----------|--------------|
| Twitch | Click **Connect** in the app — opens Twitch's own login page in your browser |
| TikTok | TikTok username + optional API key |
| YouTube | Click **Login with Google** — one-click OAuth, no API console setup required |

---

## Overlay URLs

| URL | Description |
|-----|-------------|
| `http://localhost:54473/chatbot` | Unified feed — all platforms |
| `http://localhost:54473/chatbot/{id}` | Filtered feed for a specific streamer (room use) |
| `http://localhost:54473/chatbot/dock` | Browser dock panel with send bar and mod buttons |
| `http://localhost:54473/stats-overlay` | Live viewer/follower/subscriber/member counts |
| `http://localhost:54473/logs` | Chat history log viewer |

Port is configurable in Settings (default: `54473`).

---

## Twitch Permissions — What We Ask For and Why

When you connect Twitch, plagComms opens Twitch's official OAuth page and asks for the following permissions. Nothing is collected, stored, or sent to any plagComms server — your token lives only in **Windows Credential Manager** on your own machine.

Every permission is scoped to **your channel only**. plagComms cannot access or modify any other streamer's channel.

| Permission | Plain English | Used For |
|-----------|--------------|----------|
| `chat:read` | Read chat messages from your channel | Displaying chat in the overlay and pop-out window |
| `bits:read` | See who cheered and for how much | Showing cheer/bits events in the overlay |
| `channel:read:subscriptions` | See new subs and gift subs on your channel | Showing sub and gift-sub events in the overlay |
| `channel:read:redemptions` | See channel point redemptions | Showing redemption events in the overlay |
| `moderator:read:followers` | See new follower events | Showing follow alerts in the overlay |
| `user:write:chat` | Send chat messages as you | Powers the send bar in the pop-out window and browser dock |
| `channel:manage:raids` | Start a raid from your channel to another | Powers the **⚔ Raid** button — you pick the target and confirm |
| `moderator:manage:shoutouts` | Send a `/shoutout` from your channel | Powers the **📣 Send Shoutout** button on raid alerts |
| `moderator:manage:banned_users` | Time out or ban a user from your channel | Powers the **⏱ Timeout** and **🔨 Ban** mod action buttons (off by default) |
| `moderator:manage:chat_messages` | Delete a specific chat message | Powers the **🗑 Delete** mod action button (off by default) |
| `channel:manage:polls` | Create polls in your channel | Powers the **📊 Poll** button in the pop-out toolbar |
| `moderator:manage:chat_settings` | Read and change chat mode settings | Powers the **🎛 Chat** dropdown — shows live status of sub-only, slow mode, etc. |
| `channel:moderate` + `moderation:read` | Read and perform moderation in your channel | Moderator-attributed mod-action events plus the Delete / Timeout / Ban buttons |
| `channel:read:vips` | See your channel's VIPs | Showing VIP grants/removals in mod-action events |
| `channel:read:ads` | See your ad-break schedule | The "ad incoming / ad active" drop-down notice |
| `channel:manage:ads` | Snooze (delay) your next scheduled ad | The **Skip** button on the ad-break notice |

> A few additional read-only moderation scopes (`moderator:read:*` for blocked terms, unban requests, warnings, moderators, and VIPs) are also requested so moderation events display with full detail. If you connected before these were added, re-connect Twitch to enable the newer features.

### What plagComms does NOT do

- ❌ Does not auto-moderate, auto-ban, or take any action without you clicking a button
- ❌ Does not read your subscriber list, earnings, or account settings
- ❌ Does not store your token anywhere except your own PC's Credential Manager
- ❌ Does not send your token to any plagComms server

Revoke access at any time at [Twitch → Settings → Connections](https://www.twitch.tv/settings/connections).

---

## Changelog

### 1.0.2 — 2026-06-19 — Raid Tags, YouTube Subs, Ad Notices & Fixes

- **Raid viewer tags** — chatters who arrive with a raid now get a **⚔ tag showing whose raid they came in with**, in the docked chat, pop-out, and OBS overlay. (Twitch never identifies raid viewers directly, so this works by tagging a chatter whose first message of the stream lands within ~2 minutes of a raid; the tag stays for 10 minutes from the raid.)
- **New-subscriber alerts for YouTube** — YouTube has no native subscribe event, so plagComms watches your subscriber count while live and announces new subs (named when the subscriber is public, anonymous otherwise) across the overlay and streamer chats.
- **Ad-break notifications** on your streamer chat (docked + pop-out) — an amber **"⏰ Ad in M:SS"** countdown appears about a minute before a Twitch ad, turns red **"🔴 AD BREAK · M:SS left"** while it runs, and has **Skip** and dismiss buttons. The notice floats over the top of the chat instead of pushing your messages down. *Requires a one-time Twitch re-auth to grant ad permissions.*
- **Automatic re-auth reminder** — if your saved Twitch login is missing permissions a new version needs, plagComms detects it at startup and prompts you with a one-click **Re-authenticate** button, so newly added features never silently do nothing.
- **BetterTTV emote add/remove notices** — when a BTTV emote is added or removed (including via channel-point redemptions) it's announced in your streamer chats.
- **YouTube emoji & stickers** now render as their actual image inline instead of showing up as a blank message.
- **Pop-out chat fixes** — messages with links and emotes no longer scramble or spill past the window edge, and the chat now reliably stays pinned to the newest message instead of lagging a line or two behind.
- **Shared-chat partner channels** now show their name instead of a raw numeric ID.
- **Dashboard stats persist across restarts** — follower/subscriber numbers show immediately instead of blanks until the next live update.
- **Cleaner overlay** — hid the thin scrollbar on the OBS overlay.

---

### 1.0.1 — 2026-06-05 — Horizontal Feed Fix

- **Fixed the horizontal chat-bar overlay freezing** once it filled with messages — after about six messages it stopped accepting new chat. It now scrolls continuously, with the oldest message sliding off the left as each new one arrives.

---

### 1.0.0 — 2026-06-05 — Official Release

**plagComms is a multi-platform live chat aggregator for streamers on Windows.**

It runs silently in your system tray and connects to your Twitch, TikTok Live, and YouTube Live streams simultaneously — merging all chat, events, and alerts into a single OBS browser-source overlay. No subscriptions, no cloud accounts, no backend. Everything runs locally on your machine.

---

**What it does:**

- 🟣 **Twitch** — chat (with BTTV/FFZ/7TV emotes), cheers, subs & resubs, gift subs, follows, raids, channel point redemptions, watch streak Power-Ups
- 🎵 **TikTok Live** — chat, gifts, likes, follows, shares, subscriptions, superfan events
- 🔴 **YouTube Live** — chat, Superchats, memberships (new, milestone, gifted)
- 🖥️ **OBS Overlay** — browser-source chat feed with smart auto-scroll, per-message fade, emote rendering, profile pictures, Twitch badges, and full appearance customization
- 🏠 **Multi-Streamer Rooms** — create or join a shared room with other streamers and see each other's chats merged in real time
- 💬 **Native Pop-Out Window** — dockable chat window that replays the last 200 messages
- 📊 **Session Stats** — live counters for messages, bits, subs, gifts, Superchats, and more across all platforms
- 📝 **Chat Logging** — opt-in SQLite logging with a built-in search and CSV export
- 🔒 **Secure by default** — OAuth tokens and API keys stored in Windows Credential Manager, never written to disk in plain text
- 🔌 **Add-on WebSocket** — local WebSocket endpoint lets you build custom tools that react to any stream event in real time (see `Events/ADDON_GUIDE.md`)

---

**In this release:**

- Official 1.0 — out of pre-release after months of testing and feedback from real streams
- **Consistent styling across the overlay, streamer chat (dock), and pop-out** — profile pictures, badges, gifts, subs, and events now look the same everywhere
- **TikTok badges redesigned** — diamond gifter level, FANS heart (orange for active fans, gold glow for superfans), moderator sword, and top-gifter — right-aligned and consistently ordered
- **Detailed gift display** — gift image, sender, gift name × quantity, and diamond value
- **Gigantified emotes now render at full resolution** (Twitch Power-Ups + TikTok sub emotes) instead of a blurry upscale
- **Independent per-event visibility toggles** for the overlay and the streamer chat, now applied reliably to both
- **Compact overlay mode** for a denser on-stream chat feed
- New app icon and automatic update checks
- Add-on developer documentation (`Events/EVENT_REFERENCE.md` + `Events/ADDON_GUIDE.md`)

---

### 0.9.38 — 2026-05-22 — Polish & Fixes

- **Twitch subs now carry a `sub_is_shared` flag** — `false` when the subscription payment is processed silently, `true` when the viewer clicks Share in Chat
- **Fixed emote autocomplete disappearing while typing** — Windows was auto-dismissing the popup; now uses a stable Tool window
- **Fixed emote autocomplete appearing off-screen** — popup now flips below the input bar when near the top of the display
- **Fixed scroll-wheel guard not forwarding to the page** — wheel events now correctly redirect to the scroll area viewport
- **Scroll-wheel guard now covers all spinbox types** — decimal spinboxes now protected via shared base class

### 0.9.37 — 2026-05-19 — Event Routing Fix
- Fixed dashboard not counting events when overlay toggles were off
- Fixed pop-out and dock not showing events when overlay toggles were off
- Fixed TikTok text badges not rendering in OBS (CORS issue)
- Pop-out chat window remembers its size and position

### 0.9.36 — 2026-05-11 — UI Polish
- Main window position and size now saved and restored between sessions
- TikTok status shows active username

### 0.9.35 — 2026-05-05 — YouTube Dual Stream & Google Login
- YouTube now connects to both vertical and horizontal streams simultaneously
- YouTube login simplified — one-click Google login, no API console required
- Fixed YouTube 400 error on broadcast lookup

### 0.9.34 — 2026-05-03 — Room & YouTube Fixes
- Fixed room message doubling for Twitch Shared Chat
- Fixed BTTV/FFZ emote doubling when name overlaps native Twitch emote
- Fixed YouTube never connecting with many past broadcasts
- YouTube session expired now shows a dialog with Fix Now button

### 0.9.33 — 2026-05-01 — Watch Streaks
- Twitch Watch Streak Power-Up events shown in overlay and pop-out

### 0.9.32 — 2026-04-30 — Bidirectional Chat
- Pop-out chat input bar — send messages to Twitch and YouTube
- Emote autocomplete for BTTV/FFZ/7TV
- Twitch mod actions: Delete, Timeout, Ban
- Twitch toolbar: Raid, Poll, Shoutout, Chat Mode controls
- Raid alert cards with one-click Send Shoutout

### 0.9.31 — 2026-04-28
- Fixed YouTube quota exhaustion — switched to zero-quota continuation token API
- Per-platform debug log toggles

### 0.9.30 — 2026-04-26
- TikTok badge enrichment (mod, fan club, top gifter rank, superfan)
- Fixed TikTok streakable gift combo counting

### 0.9.27 — 2026-04-25
- Channel Stats overlay (`/stats-overlay`)
- Twitch Gigantify and TikTok emote pack giant emote rendering
- VIP Highlight feature

### 0.9.3 — 2026-04-19
- TikTok custom emotes — drop PNG/GIF files into `%APPDATA%\plagComms\tiktok_emotes\`
- TikTok native bracket emotes convert to emoji automatically

### 0.9.2 — 2026-04-19
- One-click in-app auto-update

### 0.9.1 — 2026-04-19
- BTTV emote support for YouTube Live Chat
- Animated GIF emote support
- 7TV emote support for Twitch

### 0.9.0-beta — 2026-04-16
- Initial beta release

---

## License

Source is not publicly available. Distributed as a compiled Windows executable only.

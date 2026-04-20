# plagComms

**plagComms** is a multi-platform live chat aggregator and OBS overlay tool for streamers. It pulls chat from Twitch, TikTok Live, and YouTube Live into a single unified overlay — and lets multiple streamers share each other's chat in real time through a room system.

> **Current Version:** 0.9.5  
> **Platform:** Windows (standalone `.exe`)

---

## What It Does

plagComms runs silently in your system tray and serves a browser-source overlay that you drop into OBS. It connects to your live streams across platforms, merges all chat into one feed, and broadcasts it to your overlay in real time. You can also join a shared room with other streamers so their audiences' chats appear in your overlay (and yours in theirs).

---

## Features

### Platform Support
- **Twitch** — chat, cheers (bits), subscriptions, gift subs, new followers, raids, channel point redemptions
- **TikTok Live** — chat, gifts, likes, follows, shares, subscriptions
- **YouTube Live** — chat, Superchats, Super Stickers, memberships (new sponsors, milestones, gifted memberships)

### OBS Overlay
- Browser-source overlay served locally over WebSocket
- Scrollable chat feed with smart auto-scroll (pauses when you scroll up)
- Per-message fade/lifetime control and max message count
- Configurable font size, message alignment (left/right), and border styling
- Platform-colored usernames with real Twitch badge images
- BTTV and FFZ emote support with hover tooltips
- Profile picture display with local disk cache

### Multi-Streamer Room System
- Create or join a password-protected room shared with other streamers
- Real-time WebSocket relay — each streamer's chat appears in everyone's overlay
- Public room listing so you can discover open rooms
- Room members shown with platform tags (TW / TT / YT)
- Relay hub deployed on Railway (stateless, no database)

### Native Pop-Out Chat Window
- Dockable native chat window separate from the overlay
- Replays the last 200 messages on open

### Settings & Security
- Secrets (OAuth tokens, API keys) stored in Windows Credential Manager via `keyring`
- All other settings persisted to `%APPDATA%\plagComms\settings.json`
- Encrypted backup/restore — export all settings and credentials to a file, import on another PC

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
- Download link opens the GitHub releases page

### Overlay Appearance Customization
- Max messages displayed
- Message lifetime (seconds)
- Font size
- Right-align mode
- Optional border (color, width, radius)
- Per-platform accent colors (Twitch purple, TikTok red, YouTube red — all configurable)

---

## Installation

1. Download the latest `plagComms.exe` from [Releases](https://github.com/plagrizr/plagcomms/releases/latest)
2. Run the exe — no installer needed
3. plagComms starts in the system tray
4. Open the settings window from the tray icon and connect your platforms
5. Add `http://localhost:54473/chatbot` as a Browser Source in OBS

---

## Connecting Platforms

| Platform | What you need |
|----------|--------------|
| Twitch | OAuth token + Client ID + channel name |
| TikTok | TikTok API key + username |
| YouTube | OAuth Client ID + Client Secret (Google API Console) |

---

## Overlay URL

| URL | Description |
|-----|-------------|
| `http://localhost:54473/chatbot` | Unified feed — all platforms |
| `http://localhost:54473/chatbot/{id}` | Filtered feed for a specific streamer (room use) |
| `http://localhost:54473/logs` | Chat history log viewer |

Port is configurable in Settings (default: `54473`).

---

## Changelog

### 0.9.5 — 2026-04-19
- Fixed TikTok custom emotes not rendering in native chat window
- Added raw comment debug logging to `tiktok_debug.log` to diagnose emote format

### 0.9.4 — 2026-04-19
- YouTube quota exceeded now sleeps until midnight PT (exact reset time) instead of a flat 1 hour
- Quota reset timestamp is persisted to settings — app restarts no longer immediately retry when quota is still exhausted
- BTTV emotes for YouTube are now loaded once per session instead of on every reconnect attempt

### 0.9.3 — 2026-04-19
- TikTok custom emotes — drop PNG/GIF files into the `tiktok_emotes/` folder and they render as images in chat
- TikTok native bracket emotes (e.g. `[laugh]`, `[heart]`) now convert to emoji automatically
- Custom emotes reload on every TikTok reconnect — no app restart needed after adding new emotes

### 0.9.2 — 2026-04-19
- One-click in-app auto-update — install new versions without leaving plagComms

### 0.9.1 — 2026-04-19
- BTTV emote support for YouTube Live Chat
- Animated emote support (GIF emotes now animate in native chat window)
- 7TV emote support for Twitch chat
- Announcement messages now show a rainbow border highlight
- Channel point redemptions now display the reward name correctly
- YouTube API quota exceeded no longer spams the activity log
- Build script now auto-names the exe with the version number

### 0.9.0-beta — 2026-04-16
- Initial beta release
- Twitch, TikTok, YouTube Live chat support
- Multi-streamer room system with public room listing
- BTTV/FFZ emote support with hover tooltips
- Real Twitch badge images
- Scrollable overlay with smart auto-scroll
- Native pop-out chat window
- Encrypted settings backup/restore
- SQLite chat history logging

---

## License

Source is not publicly available. Distributed as a compiled Windows executable only.

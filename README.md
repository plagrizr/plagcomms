# plagComms

**plagComms** is a multi-platform live chat aggregator and OBS overlay tool for streamers. It pulls chat from Twitch, TikTok Live, and YouTube Live into a single unified overlay — and lets multiple streamers share each other's chat in real time through a room system.

> **Current Version:** 0.9.32  
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
- Secrets (OAuth tokens, API keys) stored in **Windows Credential Manager** via `keyring` — never written to disk in plain text, never sent to any plagComms server
- All other settings persisted to `%APPDATA%\plagComms\settings.json`
- Encrypted backup/restore — export all settings and credentials to a file, import on another PC
- All Twitch API calls go directly from your machine to `api.twitch.tv` — plagComms has no backend

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
| Twitch | Click **Connect** in the app — opens Twitch's own login page in your browser |
| TikTok | TikTok API key + username |
| YouTube | OAuth Client ID + Client Secret (Google API Console) |

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
| `moderator:read:followers` | See new follower events | Showing follow alerts in the overlay (Twitch requires this specific scope for the v2 followers API even on your own channel) |
| `channel:manage:raids` | Start a raid from your channel to another | Powers the **⚔ Raid** button in the pop-out chat window — you pick the target and confirm before anything is sent |
| `moderator:manage:shoutouts` | Send a `/shoutout` from your channel | Powers the **📣 Send Shoutout** button that appears when someone raids you — never fires automatically |
| `moderator:manage:banned_users` | Time out or ban a user from your channel | Powers the **⏱ Timeout** and **🔨 Ban** mod action buttons in the pop-out chat window (optional, off by default) |
| `moderator:manage:chat_messages` | Delete a specific chat message | Powers the **🗑 Delete** mod action button in the pop-out chat window (optional, off by default) |
| `channel:manage:polls` | Create polls in your channel | Powers the **📊 Poll** button in the pop-out chat toolbar |
| `moderator:manage:chat_settings` | Read and change chat mode settings (sub-only, slow mode, etc.) | Powers the **🎛 Chat** dropdown in the pop-out toolbar — shows live status and lets you toggle each mode |

### What plagComms does NOT do

- ❌ Does not read or write your chat messages (no `chat:edit` or `chat:write`)
- ❌ Does not ban, time out, or moderate your chat
- ❌ Does not read your subscriber list, earnings, or account settings
- ❌ Does not store your token anywhere except your own PC's Credential Manager
- ❌ Does not send your token to any plagComms server — all API calls go directly from your machine to `api.twitch.tv`

If you're uncomfortable with any permission, you can revoke plagComms' access at any time under [Twitch → Settings → Connections](https://www.twitch.tv/settings/connections).

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

### 0.9.32 — 2026-04-30 — Bidirectional Chat
- **Pop-out chat input bar** — plagComms is now bidirectional; type and send messages to your live chat without leaving the app
- **Platform selector**: choose All connected, Twitch only, or YouTube only; unconfigured platforms are omitted from the list; configured-but-offline platforms are greyed; TikTok listed as coming soon
- Chat input auto-grows from one line up to four lines as text wraps; **Enter** sends, **Shift+Enter** inserts a newline; send button greys out when the box is empty
- **Emote autocomplete**: type `:` followed by 2+ characters to see a popup of matching BTTV/FFZ/7TV emotes; Up/Down navigates, Enter or Tab inserts, Escape dismisses; clicking a row also inserts
- **Twitch send**: `POST /helix/chat/messages` — requires re-authentication to pick up the new `user:write:chat` scope
- **YouTube send**: `liveChatMessages.insert` — requires re-authentication to pick up the new `youtube.force-ssl` scope; only available while live
- Fixed "Failed to load Python DLL" error dialog during in-app updates — updater now uses `os._exit(0)` to bypass PyInstaller DLL cleanup
- Pop-out chat: incoming raids now show a prominent shoutout card with raider name, viewer count, and a one-click **Send Shoutout** button
- Fixed duplicate raid cards — IRC and EventSub no longer both fire; the richer EventSub card (with viewer count) is shown
- Pop-out chat: new **⚔ Raid** button in the toolbar — search a channel, preview their avatar, then a 30-second countdown fires the raid (RAID NOW to go immediately)
- Pop-out chat: new **📊 Poll** button in the toolbar — question, up to 5 choices, duration picker, creates a Twitch poll in one click
- Pop-out chat: new **Mod Actions** — enable "Show mod action buttons" in Twitch settings to add 🗑 Delete, ⏱ Timeout (with duration picker), and 🔨 Ban to every message; off by default; Delete propagates to native Twitch chat, the pop-out, and the HTML/OBS overlay
- Fixed raider tags not appearing — raiding streamer detected by username match; raid viewers detected via `badges` tag (`raid/1`); both tagged **⚔ Raider · channelname** for 10 minutes
- Pop-out chat: new **🎛 Chat** button in toolbar — live sub-only, emote-only, unique-chat, follower-only, and slow-mode toggles; auto-polls every 10 seconds so moderator changes from Twitch are reflected in real time
- Twitch OAuth: added `moderator:manage:banned_users`, `moderator:manage:chat_messages`, `channel:manage:polls`, `moderator:manage:chat_settings`, and `user:write:chat` scopes (re-auth required)
- YouTube OAuth: scope upgraded from `youtube.readonly` to `youtube.force-ssl` (re-auth required to enable chat sending)
- Fixed YouTube reconnecting every 10–80 seconds during quiet streams — staleness detection now time-based (4 minutes)
- Fixed YouTube polling too aggressively pre-stream — interval raised to 90 s
- Fixed YouTube duplicate messages on reconnect — deduplication by message ID across reconnects

### 0.9.31 — 2026-04-28
- **Fixed YouTube quota exhaustion** — YouTube chat now uses the internal continuation-token API (same as the YouTube web player), which has zero quota cost. OAuth is now used only to find the broadcast video ID on connect (1 unit) and fetch channel stats every 3 minutes (~576 units/day total vs ~86,400 previously). Chat messages, superchats, and memberships all flow through the continuation path
- **Per-platform debug log toggles** — Twitch Settings now has separate toggles for Twitch IRC/EventSub, TikTok badge/gift verbose logs, and YouTube chat debug. All off by default; TikTok toggle gates the tiktok_debug.log file level directly

### 0.9.30 — 2026-04-26
- TikTok badge enrichment — mod, fan club level, top gifter rank, title gifter, diamond level, super fan status extracted from badge_list and shown as badges on all TikTok events in both overlay and pop-out; icon images pulled from combine_badge_struct; text pills for badges without icons
- Fixed TikTok gift combos: repeat_count is cumulative so streakable gifts now fire once when the streak ends with the correct total (8 roses → ×8, not ×44); non-streakable gifts debounce over 1.5s
- Pop-out chat: TikTok text badges (no icon URL) render as small labeled pills instead of invisible empty boxes

### 0.9.29 — 2026-04-25
- BTTV/FFZ/7TV emotes now refresh every 15 minutes while connected — emotes added mid-stream are picked up automatically without reconnecting
- Fixed YouTube going stale mid-stream — polling interval capped at 30s, staleness watchdog forces reconnect after 4 minutes of no response, fail threshold halved for faster detection
- Fixed YouTube quota wait message spamming the activity log — now logged once when quota gate is entered instead of every 5 minutes

### 0.9.28 — 2026-04-25
- Fixed Twitch emote quality — all native Twitch emotes now use the 3.0 CDN resolution (112px) instead of 1.0 (28px); giant Gigantify emotes are now crisp instead of blurry
- Fixed blank space below Gigantify giant emote in pop-out chat
- Fixed Gigantify emote in OBS overlay stretching to full row width — now renders as a square sized to match message height
- Fixed horizontal chat overlay: Gigantify now shows as a 2x-size inline emote in the chip (was silently dropped)
- Fixed horizontal chat overlay: emote tooltip text (name + "Twitch") was rendering inline — emotes now show as image-only
- Fixed horizontal chat overlay: VIP Highlight now pulses the chip border with the rainbow glow and fades after the configured duration
- VIP Highlight now also applies to Gigantify emote messages, not just regular chat

### 0.9.27 — 2026-04-25
- **New OBS overlay: Channel Stats** — add `http://localhost:54473/stats-overlay` as a Browser Source to display live follower, subscriber, and viewer counts across Twitch, TikTok, and YouTube; platform cards auto-hide when not connected
- **Dashboard** — each platform card now shows live channel stats above session counters
- Fixed TikTok viewer count showing cumulative unique viewers (~19.8K) instead of concurrent — now uses `m_total` from `RoomUserSeqEvent`
- Added TikTok **Follower count** and **SuperFan count** to dashboard and stats overlay
- Fixed TikTok viewer count and channel card not appearing in the stats overlay on connect
- **Twitch: Gigantify an Emote** — correctly detected via `msg-id=gigantified-emote-message`; emote renders at ~10x size in both pop-out chat and OBS overlay; messages with text before the emote show the text above it
- TikTok emote-pack claims (EmoteChatEvent) also render giant using the same pipeline
- Fixed emote messages in pop-out chat causing horizontal overflow — emote rows no longer push the window wider than its bounds
- **YouTube: Reconnect button** — appears when YouTube drops connection so you can restart the listener without re-authenticating; also shows on token-invalid errors
- **VIP Highlight** — enter comma-separated usernames in Appearance → VIP Highlight; those users' messages rainbow-cycle (gold → pink → cyan → green) in pop-out and OBS overlay, auto-fading after a configurable number of seconds
- **Twitch debug mode** — enable "Log all Twitch events" on the Twitch page to print every raw IRC line and EventSub notification to the activity log

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

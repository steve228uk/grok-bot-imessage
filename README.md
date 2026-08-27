# Grok Bot iMessage on Mac

iMessage/SMS skill for Grok Bot on the **user's Mac** via [`imsg`](https://imsg.sh). Read history, handle OTPs, and send messages — always with a **confirm-before-send** widget. Never auto-send.

Mac-only. Grok Bot must run this install chat on the user's Mac (not on Grok Bot's Linux computer).

## Install

Paste this to Grok Bot **on your Mac**:

```
Install Grok Bot iMessage from https://github.com/steve228uk/grok-bot-imessage
Follow INSTALL.md.
```

Grok Bot will check you are on macOS, fetch and save the skill, optionally ask to install `imsg` via Homebrew on **your Mac**, and remind you about Full Disk Access. It will not create a new teammate or install anything on Grok Bot's own machine.

If `imsg` is missing or permissions are denied, you can still paste OTP codes in chat — installs do not block live logins.

## What's in the pack

| Path | What |
|---|---|
| `skills/imessage-on-mac.md` | Skill: read/send/triage iMessage via `imsg` on the user's Mac, confirm-before-send, OTP cleanup |
| `INSTALL.md` | The installer prompt Grok Bot follows |

## Requirements (user's Mac)

- macOS with Messages.app signed in
- Optional: `imsg` — `brew install steipete/tap/imsg` (usually `/opt/homebrew/bin/imsg` or `/usr/local/bin/imsg`)
- **Full Disk Access** for the app that runs `imsg` (to read `~/Library/Messages/chat.db`)
- **Automation** for Messages.app if sending or deleting

Docs: https://imsg.sh · Repo: https://github.com/openclaw/imsg

## Behavior

- All `imsg` commands run on the **user's Mac** via ExternalShell — not on Grok Bot's Linux box
- **Confirm-before-send** widget before every outbound message (primary Send, danger Don't send)
- OTPs: prefer iMessage; paste-in-chat fallback; delete the OTP message after use (not if pasted)
- Quiet triage: an existing teammate (e.g. Keeper) may own it; this pack does not add one

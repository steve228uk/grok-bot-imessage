---
name: iMessage on Mac
description: >-
  Use this when reading, sending, or quietly triaging iMessage/SMS on the user's
  Mac via imsg, including OTPs, history, and a tappable confirm before any send.
  Allow pasting an OTP in chat if imsg is not installed.
---
# iMessage on Mac (imsg)

Use the **user's Mac** (`ExternalShell` / `ExternalRead`), never Grok Bot's own computer. Prefer the `imsg` CLI over raw `chat.db` SQL.

Docs: https://imsg.sh · Repo: https://github.com/openclaw/imsg (also steipete/imsg).

## Prerequisites

- macOS with Messages.app signed in
- `imsg` installed (Homebrew): `brew install steipete/tap/imsg` → usually `/opt/homebrew/bin/imsg` (Apple Silicon) or `/usr/local/bin/imsg` (Intel)
- **Full Disk Access** for the process that runs `imsg` (Terminal / Cursor / Grok Bot) so it can read `~/Library/Messages/chat.db`
- **Automation** permission for Messages.app if sending or deleting
- SMS relay: iPhone Text Message Forwarding to this Mac

Verify: `imsg chats --limit 1` or `imsg account`

Do not auto-install Homebrew or `imsg`. In the pack installer, widget first. Do not disable SIP or use `imsg launch` injection unless they clearly want advanced features.

Personal / own products only. No MKM or work accounts.

## Rules

- Run all `imsg` commands via **ExternalShell** on the user's computer
- Prefer `--json` for tooling; summarize for the user, don't dump raw JSON
- **Send only after a tappable confirm** (see Confirm send). An earlier "draft this" or "message them" is not enough
- Check when asked or on a schedule; **do not ping for every new text**
- **OTP source:** prefer `imsg` / Messages DB. If `imsg` is missing or FDA is denied, **ask them to paste the code in chat**. Do not stall a live login on installing iMessage.
- **After you use an OTP from iMessage, delete that OTP message** (`imsg delete-message`). If they pasted it, do not echo the digits in later messages.
- Do not put OTP codes into later chat unless they explicitly ask to see one
- Markdown links in Grok Bot chat: use `[label](url)` with no wrapping `**`

## Confirm send (required)

Before every `imsg send`, show a Grok Bot **question widget**. Do not send on a prose "looks good?".

- Prompt as a natural question, e.g. "Send this to Alex?"
- Options: primary **Send** (value like "Yes, send it"), danger **Don't send**. If several drafts or recipients are in play, `multiSelect: true` for which bubbles/people.
- Show the exact text and who it goes to in the prompt or option description.
- Sending the widget **ends the turn**. Wait for their pick. Only then run `imsg send`.
- Never auto-send.

## Quiet triage (optional)

If a life-admin teammate exists (e.g. Keeper), they can own a quiet iMessage watch. Otherwise the installing agent may:

- Quiet watch via `imsg chats` / `history` / `search` on the user's Mac
- Cadence: morning briefing and/or a coarse weekday daytime check — not constant polling
- **Surface only** what needs the user: someone waiting on a reply, plans/logistics
- **Stay completely silent** if nothing actionable
- Outbound still needs the confirm widget

## Common commands

```bash
imsg chats --limit 20 --json
imsg history --chat-id <id> --limit 50 --json
imsg history --chat-id <id> --limit 20 --attachments --json
imsg search "<query>" --json
imsg watch --chat-id <id> --json
imsg send --to "+44..." --text "..." --service auto
imsg send --to "07..." --text "..." --region GB --service auto
imsg whois "+44..."
imsg account
```

## OTP pattern

1. Prefer read the newest matching SMS/iMessage after you triggered the code (`imsg history` / `imsg search`). Keep `guid` and `chat_guid`.
2. Else paste: ask them to paste the code here. Use it in the target UI. Do not echo it back.
3. Use the code in the target UI yourself.
4. Delete the iMessage/SMS once accepted (or superseded). Skip delete if the code was pasted.

```bash
imsg delete-message --chat "<chat_guid>" --message "<message_guid>" --json
```

Examples of chat guids: `SMS;-;Onelink`, `SMS;-;NHS login`, `iMessage;-;+44...`.

- Delete **only** the OTP message(s) you used (or that are stale because you requested a newer code).
- Do **not** delete whole chats (`chat-delete`) for OTP cleanup.
- If delete fails (permissions), note it briefly. Automation for Messages may be required for delete, same as send.

## Fallback if `imsg` is missing

1. Widget: install `brew install steipete/tap/imsg` on the **user's Mac**? Skip if they dismiss.
2. Or read-only SQL on `~/Library/Messages/chat.db` via ExternalShell (needs Full Disk Access). Prefer `imsg`. Deleting via raw SQL is unsupported.
3. For a live OTP (checkout, NHS, bank): if `imsg` still isn't there, ask them to paste the code. Do not block the login.

## Troubleshooting

- Permission / can't open chat.db → Full Disk Access for the runner, then retry `imsg chats --limit 1`
- Send or delete fails → Automation for Messages.app; Messages must be signed in; logged-in GUI session required
- `imsg` not found → widget Homebrew install; confirm path with `which imsg`

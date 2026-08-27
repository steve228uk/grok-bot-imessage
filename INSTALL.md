# INSTALL.md — Grok Bot iMessage on Mac

This file is a **prompt for Grok Bot**. If you are Grok Bot, follow it now. Do not dump this file back as documentation.

## Command the user can paste

```
Install Grok Bot iMessage from https://github.com/steve228uk/grok-bot-imessage
Follow INSTALL.md.
```

## What you are installing

An iMessage pack for Grok Bot:

- Skill (save as a user skill):
  - `skills/imessage-on-mac.md` → id `imessage-on-mac`
- Optional on the **user's Mac**: `imsg` CLI via Homebrew (`brew install steipete/tap/imsg`)

This pack has **no npm CLI**. Grok Bot has npm, not pnpm. Do not invent or install a CLI for this pack.

Do **not** create a new teammate. Keeper (or another existing life-admin agent) already owns quiet iMessage triage if they have one.

## Steps

### 1. Mac gate — user's computer, not yours

Grok Bot's own computer is Linux. `imsg` belongs on the **user's Mac**, not here.

On the **user's computer**, run via ExternalShell (or equivalent):

```bash
uname -s
```

- If the result is **not** `Darwin`, **stop**. Tell them this pack only works on macOS. They must open this Grok Bot chat **on their Mac** (Cursor, Terminal, or the Grok Bot Mac app) and paste the install command there. Do not install Homebrew or `imsg` on Grok Bot's Linux box.
- If `Darwin`, continue.

### 2. Fetch (do not clone a working tree unless they asked)

Read this raw file (jsDelivr or GitHub raw is fine):

- https://cdn.jsdelivr.net/gh/steve228uk/grok-bot-imessage@main/skills/imessage-on-mac.md

If a skill with id `imessage-on-mac` already exists, overwrite it with the fetched body.

Save the skill with Grok Bot skill-write using id `imessage-on-mac`, the `name` and `description` from the YAML frontmatter, and the markdown body.

### 3. `imsg` CLI — widget, then stop

On the **user's Mac** (ExternalShell), check whether `imsg` is already available:

```bash
command -v imsg && imsg chats --limit 1
```

Typical paths: `/opt/homebrew/bin/imsg` (Apple Silicon) or `/usr/local/bin/imsg` (Intel). `imsg account` also works for a quick sanity check.

If `imsg` works, skip the widget and say it is already there. Remind them about permissions (step 4) if commands fail.

If `imsg` is missing or not working, send a Grok Bot question widget (**this ends the turn**). Do not install until they answer.

- `prompt`: "Install imsg on your Mac? It lets me read and send iMessage/SMS with your confirm-before-send. Skip if you only want OTP paste-in-chat for now."
- Options:
  - `{ "label": "Install imsg", "value": "Install imsg on my Mac", "style": "primary" }`
  - `{ "label": "Skip", "value": "Don't install imsg" }`

If they picked Install imsg, on the **user's Mac** (ExternalShell):

```bash
brew install steipete/tap/imsg
imsg chats --limit 1
```

Do not auto-install Homebrew. If `brew` is missing, tell them to install Homebrew on their Mac first, or skip and use OTP paste fallback.

If they skip, or install fails, note that OTP and other flows can still use **paste the code in chat** — do not stall a live login on installing iMessage.

### 4. macOS permissions (tell them once)

On the user's Mac, `imsg` needs:

- **Full Disk Access** for the process that runs `imsg` (Terminal / Cursor / Grok Bot) so it can read `~/Library/Messages/chat.db`
- **Automation** for Messages.app if sending or deleting messages

If `imsg chats --limit 1` fails with a permission error, explain FDA (and Automation if send/delete will be used), then retry after they grant it.

### 5. Done

Tell them:

- Skill installed: **iMessage on Mac** (`imessage-on-mac`).
- Whether `imsg` is working on their Mac (or that they skipped / need FDA).
- Outbound messages always need a **confirm-before-send** widget — you never auto-send.
- For OTPs: you prefer reading from iMessage; if `imsg` is missing or FDA is denied, they can paste the code in chat.
- Quiet triage stays with an existing teammate (e.g. Keeper) if they have one; you did not create a new agent.

## Guardrails

- Never install `imsg` or Homebrew on Grok Bot's own Linux computer.
- Never auto-install `imsg`. Widget first; skip is fine.
- Never `CreateAgent` during install.
- Never clone the repo unless they asked for a working tree.
- Never auto-send iMessage. Confirm-before-send widget every time.
- OTP fallback: if `imsg` is missing or FDA denied, ask them to paste the code. Do not block a live login.
- Grok Bot has npm, not pnpm. This pack has no npm CLI — do not invent one.
- Personal / own products only. No MKM or work accounts.

# 🛡 Serena Group Manager

A Telegram group moderation bot built for **Telegram Serverless** using the `@tgcloud/cli`. It provides practical moderation commands, persistent group settings, warning history, a button-based menu, and step-by-step deployment instructions for Android Termux and desktop terminals.

> **Hosting model:** after a successful deployment, Telegram runs the bot's supported modules on its infrastructure. Termux is used to edit and deploy code; it does not need to stay open.

## Features

- **Friendly menu:** `/start`, `/menu`, `/help`, and `/commands`
- **Member moderation:** reply-based ban, unban, kick, timed mute, and unmute
- **Warning system:** add warnings, review warning history, clear warnings
- **Message tools:** purge up to 100 recent messages, pin and unpin
- **Group settings:** persistent rules and welcome messages on/off
- **Admin tools:** list administrators, view chat/user IDs, check bot status and version
- **Persistent storage:** built-in SQLite database for rules, welcome preferences, and warnings
- **Inline buttons:** uses supported button-style hints; the final appearance depends on the Telegram client
- **Safety checks:** refuses to moderate group administrators, checks the command user's admin status, and reports common permission failures

## Commands

### General commands

| Command | What it does |
|---|---|
| `/start`, `/menu` | Open the main menu |
| `/help`, `/commands` | Show the command guide |
| `/version` | Show the bot version |
| `/status` | Check bot identity, group role and relevant permissions |
| `/id` | Show chat ID and your user ID |
| `/admins` | List group administrators |
| `/rules` | View saved group rules |

### Group administrator commands

Reply to the target member's message for member-specific commands. These commands are intended for group administrators.

| Command | Usage |
|---|---|
| `/ban` | Reply to a member's message to ban them |
| `/unban` | Reply to a message from the user to remove their ban |
| `/kick` | Reply to a member's message to remove them while allowing them to rejoin |
| `/mute 10` | Reply to a member's message to mute them for 10 minutes; default is 60 |
| `/unmute` | Reply to a muted member's message to restore group-default permissions |
| `/warn reason` | Record a warning and optional reason |
| `/warnings` | View warning history |
| `/clearwarns` | Clear a member's warning history |
| `/purge 10` | Delete up to 10 messages immediately before the command; maximum 100 |
| `/pin` | Reply to a message to pin it |
| `/unpin` | Unpin the current pinned message |
| `/setrules Be respectful and do not spam.` | Save the group's rules |
| `/welcome on` / `/welcome off` | Enable or disable new-member welcome messages |

**Telegram limitations:** bots cannot moderate group owners or administrators. Message deletion, bans/restrictions and pinning require the relevant administrator permissions. Telegram may reject actions for messages or users that are no longer eligible.

## Quick deployment

### Requirements

- Git
- Node.js 18+ and npm
- A Telegram bot created with [@BotFather](https://t.me/BotFather)
- Telegram Serverless enabled for that bot
- The bot's **CLI Access token** from BotFather → your bot → Serverless → CLI Access

The CLI Access token is different from the regular Telegram Bot API token. Never commit or share either token.

### Android Termux

```bash
pkg update
pkg upgrade
pkg install git nodejs
git clone https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git
cd Serena-Group-Management-
npm install
npx tgcloud login
npx tgcloud status
npx tgcloud push
npx tgcloud migrate
npx tgcloud webhook
```

During `npx tgcloud login`, use the **CLI Access token**. Review migration prompts before confirming.

For the full instructions—including updates, redeployment, Termux paste troubleshooting, Linux, macOS, and Windows PowerShell—read **[deployment termux.md](deployment%20termux.md)**.

## After deployment

1. Open the correct bot in Telegram and send `/start`.
2. Add the bot to a test group or supergroup.
3. Promote it to administrator.
4. Grant only the permissions you need:
   - **Delete messages** for `/purge`
   - **Ban/restrict members** for `/ban`, `/kick`, `/mute`, and `/unmute`
   - **Pin messages** for `/pin` and `/unpin`
5. Test moderation commands in the test group before using the bot in a live community.

You can configure Telegram's command suggestions through @BotFather → `/setcommands`. Suggested commands are a user-interface convenience; they do not grant permissions.

## Repository layout

```text
Serena-Group-Management-/
├── tgcloud/
│   ├── handlers/
│   │   ├── message.js
│   │   └── callback_query.js
│   ├── lib/
│   │   └── ui.js
│   └── schema.js
├── docs/
│   ├── TELEGRAM_SERVERLESS_GUIDE.md
│   ├── TERMINAL_DEPLOYMENT.md
│   └── tgcloud-sdk.md
├── .github/workflows/validate.yml
├── package.json
├── tgcloud.jsonc
├── .gitignore
├── README.md
├── SECURITY.md
└── deployment termux.md
```

Only supported JavaScript modules in the `tgcloud/` runtime folders are deployed as modules. The README, docs, package manifest and local `.tgcloud/` credentials are not runtime handlers.

## Update and redeploy

From the repository root:

```bash
git pull origin main
npm install
npx tgcloud status
npx tgcloud diff
npx tgcloud push
npx tgcloud migrate
npx tgcloud status
npx tgcloud webhook
```

Review the diff and migration prompts before applying changes. Code deployment and database migration are separate steps.

## Documentation

- [Telegram Serverless guide](docs/TELEGRAM_SERVERLESS_GUIDE.md)
- [Terminal deployment guide](docs/TERMINAL_DEPLOYMENT.md)
- [tgcloud SDK reference](docs/tgcloud-sdk.md)
- [Security policy](SECURITY.md)

## License

MIT. See [LICENSE](LICENSE).
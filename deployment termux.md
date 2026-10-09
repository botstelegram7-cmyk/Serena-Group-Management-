# Serena Group Manager — Termux Deployment Guide

This is the detailed Android Termux guide for the standalone repository:

`https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git`

The bot runs on Telegram Serverless after deployment. Termux is only needed to edit code, install the local CLI, and deploy updates. You can close Termux after a successful deployment.

For desktop instructions and a wider CLI reference, see [docs/TERMINAL_DEPLOYMENT.md](docs/TERMINAL_DEPLOYMENT.md). For platform behavior, runtime limits, database and Mini Apps, see [docs/TELEGRAM_SERVERLESS_GUIDE.md](docs/TELEGRAM_SERVERLESS_GUIDE.md).

## 1. One-time requirements

- Android with Termux installed from a trusted source
- Git
- Node.js 18+ and npm
- Telegram bot created with @BotFather
- Serverless enabled for the bot
- Its **CLI Access token** from BotFather → your bot → Serverless → CLI Access

**Do not use the normal Bot API token in place of the CLI Access token.** Never send either token to anyone or commit it to GitHub.

## 2. Install/update Termux packages

```bash
pkg update
pkg upgrade
pkg install git nodejs
```

Verify:

```bash
git --version
node --version
npm --version
```

Node.js must be version 18 or newer.

### If you get mirror 404 errors

1. Run `termux-change-repo`.
2. Choose **Single mirror** or the default mirror group.
3. Select a working official mirror from the list.
4. Return to the shell and run:

```bash
pkg update
pkg upgrade
```

If the package manager asks for confirmation, type `y` and press Enter. If downloads still fail, run `termux-change-repo` again and select another working mirror.

## 3. Clone the repository

For a new installation:

```bash
cd ~
git clone https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git
cd Serena-Group-Management-
```

Confirm that you are in the project root:

```bash
pwd
ls
git status
```

You should see `package.json`, `tgcloud.jsonc`, `tgcloud/`, `README.md`, and `deployment termux.md`.

### If you already cloned the repository

Do not clone it again. Navigate to your existing folder:

```bash
cd ~/Serena-Group-Management-
git status
git remote -v
git pull origin main
```

If your clone is somewhere else, use the actual path printed by your previous `pwd` command.

## 4. Install project dependencies

From the repository root:

```bash
npm install
```

This installs the local CLI dependency. It does not deploy the bot.

## 5. Link the project to your Telegram bot

In @BotFather:

1. Select the bot you want Serena Group Manager to use.
2. Enable **Serverless** if it is available for that bot.
3. Open **Serverless → CLI Access → Access token** and copy the CLI token.

Back in Termux, from the repository root, run:

```bash
npx tgcloud login
```

Paste the **CLI Access token** into the hidden prompt. If login reports that the project was linked to an app, continue to deployment.

The CLI state is stored locally under `.tgcloud/`. This directory is intentionally ignored by Git. Do not delete it as a first troubleshooting step.

## 6. Deploy the bot for the first time

Run these commands from the project root:

```bash
npx tgcloud status
npx tgcloud push
npx tgcloud migrate
npx tgcloud status
npx tgcloud webhook
```

What they do:

- `status` checks the local/cloud state.
- `push` uploads the supported code modules.
- `migrate` reviews and applies database schema changes.
- `webhook` checks the platform-managed webhook state.

Read migration prompts carefully before confirming. Code push and database migration are separate operations.

## 7. Set up the bot in Telegram

1. Open the bot in Telegram and send `/start`.
2. Add it to a test group or supergroup.
3. Promote it to administrator.
4. Enable only the permissions you need:
   - **Delete messages** — `/purge`
   - **Ban/restrict members** — `/ban`, `/kick`, `/mute`, `/unmute`
   - **Pin messages** — `/pin`, `/unpin`
5. Test `/help`, `/rules`, `/setrules`, `/welcome on`, and the moderation commands.

For member-targeted commands, reply to a message from the target member first. Telegram does not let bots moderate group owners or administrators.

## 8. Update and redeploy later

Whenever code changes are pushed to GitHub, run the following from your repository root:

```bash
cd ~/Serena-Group-Management-
git pull origin main
npm install
npx tgcloud status
npx tgcloud diff
npx tgcloud push
npx tgcloud migrate
npx tgcloud status
npx tgcloud webhook
```

Review the local/cloud diff before pushing if you have made local edits. Review migration prompts before confirming them.

Do not use `git reset --hard`, `git clean -fdx`, or `npx tgcloud push --force` as routine troubleshooting commands. They can discard local work or overwrite cloud changes.

## 9. If the hidden token prompt does not accept typing or paste

Try the following in order:

1. Make sure the terminal is focused on the hidden token prompt.
2. Long-press inside Termux and choose **Paste**.
3. If it still does not accept input, press Ctrl+C to cancel and start `npx tgcloud login` again.
4. If your installed CLI supports the documented `TGCLOUD_TOKEN` environment variable, enter the token without displaying it:

```bash
read -rsp "CLI Access token: " TGCLOUD_TOKEN
echo
export TGCLOUD_TOKEN
npx tgcloud status
npx tgcloud push
unset TGCLOUD_TOKEN
```

This method does not print the token while you enter it. Do not type the secret as a literal command argument, save it in a source file, send it in chat, or include it in a screenshot. If the method fails, share the error text with all secrets removed.

If you successfully logged in before but now see **No CLI access token found**, check that you are in the correct project directory and that the local `.tgcloud/` directory still exists. Run `npx tgcloud login` again if needed.

## 10. Commands reference

### Bot commands

| Command | Purpose |
|---|---|
| `/start`, `/menu` | Main menu |
| `/help`, `/commands` | Command guide |
| `/version` | Version |
| `/status` | Bot and permission status |
| `/id` | Chat/user ID |
| `/admins` | Group admin list |
| `/rules` | View group rules |
| `/setrules TEXT` | Set rules (admin) |
| `/welcome on` / `/welcome off` | Toggle welcomes (admin) |
| `/ban`, `/unban`, `/kick` | Reply-based member moderation |
| `/mute 10`, `/unmute` | Reply-based mute management |
| `/warn REASON`, `/warnings`, `/clearwarns` | Warning management |
| `/purge 10` | Delete up to 100 preceding messages |
| `/pin`, `/unpin` | Pin management |

### Useful CLI commands

| Command | Purpose |
|---|---|
| `npx tgcloud login` | Link project to a bot |
| `npx tgcloud status` | Check local/cloud status |
| `npx tgcloud diff` | Review differences |
| `npx tgcloud push` | Deploy modules |
| `npx tgcloud migrate` | Review/apply schema changes |
| `npx tgcloud migrate --dry-run` | Preview migrations |
| `npx tgcloud webhook` | Inspect webhook state |
| `npx tgcloud --help` | Show commands for the installed CLI |

## 11. Common errors

| Problem | Fix |
|---|---|
| `No CLI access token found` | Confirm project directory; run `npx tgcloud login` with CLI Access token |
| `404 Not Found` from Termux mirror | Run `termux-change-repo`, choose another mirror, then `pkg update` |
| Bot does not respond | Check `npx tgcloud status`, `npx tgcloud webhook`, correct bot and deployed handler |
| Import error | Use supported SDK imports and relative project imports ending in `.js` |
| Database changes pending | Review `npx tgcloud migrate --dry-run` before applying |
| Moderation command rejected | Grant the specific Telegram admin permission; confirm target is not an admin |
| Buttons do not show colours | Telegram client versions may render button styles differently |

## 12. Important Serverless notes

- Termux is not the bot's always-on server. The deployed code runs on Telegram's platform.
- Only supported runtime modules under `tgcloud/` are deployed as handlers.
- Do not assume arbitrary npm packages or filesystem APIs are available inside the Serverless runtime.
- Use the built-in database for persistent state instead of global variables.
- The code push and schema migration are separate steps.
- Check the official [Telegram Serverless guide](https://blogfork.telegram.org/bots/serverless) for current platform limitations and feature availability.

## Security checklist

- Never commit `.tgcloud/`, Bot API tokens or CLI Access tokens.
- Do not share credentials in chat, screenshots or GitHub issues.
- Test moderation commands in a group where you have authorization.
- Review code changes and database migrations before deploying to a live group.
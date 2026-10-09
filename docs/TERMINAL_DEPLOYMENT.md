# Terminal Deployment Guide

This guide covers Android Termux, Linux, macOS and Windows PowerShell for this repository. All platforms use the same `tgcloud` lifecycle; installation and directory navigation differ.

Official references:
- [Telegram Serverless guide](https://blogfork.telegram.org/bots/serverless)
- [Repository Serverless guide](TELEGRAM_SERVERLESS_GUIDE.md)
- [tgcloud SDK reference](tgcloud-sdk.md)

## Requirements

1. A bot created through [@BotFather](https://t.me/BotFather).
2. Serverless enabled for that bot.
3. The **CLI Access token** from BotFather → your bot → Serverless → CLI Access.
4. Git and Node.js 18+ with npm.

The CLI Access token is different from the normal Bot API token. Never publish either secret.

## Android Termux

### Install tools

```bash
pkg update
pkg upgrade
pkg install git nodejs
git --version
node --version
npm --version
```

If a package mirror returns 404, run `termux-change-repo`, select a working official mirror, and retry `pkg update`.

### Clone the repository

For a fresh installation:

```bash
cd ~
git clone https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git
cd Serena-Group-Management-
```

If it is already cloned, do not clone it again. Open the existing directory and verify it:

```bash
cd ~/Serena-Group-Management-
pwd
git status
git remote -v
git pull origin main
```

If your repository is in a different location, substitute that path.

### First deployment

```bash
npm install
npx tgcloud login
npx tgcloud status
npx tgcloud push
npx tgcloud migrate
npx tgcloud status
npx tgcloud webhook
```

When prompted, paste the **CLI Access token** for the bot you want to link. Review migration prompts before confirming.

### Update and redeploy

Run these commands from the repository root:

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

Review local/cloud differences before pushing if you have local changes. Code deployment and database migrations are separate steps.

### Token prompt will not accept pasted text

Try Termux long-press → **Paste** while the hidden token prompt is active. If the prompt is unusable, the CLI documents `TGCLOUD_TOKEN` for CI/environment use. This avoids displaying the token in the terminal:

```bash
read -rsp "CLI Access token: " TGCLOUD_TOKEN
echo
export TGCLOUD_TOKEN
npx tgcloud status
npx tgcloud push
unset TGCLOUD_TOKEN
```

Use this only with a CLI version that supports the environment-variable method. Do not put the token directly in the command line, a committed file, screenshot or chat. If the method fails, share only the error text after removing secrets.

## Linux (Debian/Ubuntu)

Install Git and a supported Node.js 18+ release. The following package commands work only if the configured distribution repositories provide a recent enough Node.js:

```bash
sudo apt update
sudo apt install git nodejs npm
git --version
node --version
npm --version
```

Clone and deploy:

```bash
git clone https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git
cd Serena-Group-Management-
npm install
npx tgcloud login
npx tgcloud status
npx tgcloud push
npx tgcloud migrate
```

If the repository Node.js version is older than 18, install a supported release from the official Node.js instructions before continuing.

## macOS

Install Git and Node.js 18+ using the official Node.js installer or a trusted package manager. Then:

```bash
git clone https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git
cd Serena-Group-Management-
npm install
npx tgcloud login
npx tgcloud status
npx tgcloud push
npx tgcloud migrate
```

## Windows PowerShell

Install Git for Windows and Node.js 18+ with npm. Open a new PowerShell window:

```powershell
git clone https://github.com/botstelegram7-cmyk/Serena-Group-Management-.git
Set-Location .\Serena-Group-Management-
npm install
npx tgcloud login
npx tgcloud status
npx tgcloud push
npx tgcloud migrate
```

Update later:

```powershell
git pull origin main
npm install
npx tgcloud status
npx tgcloud diff
npx tgcloud push
npx tgcloud migrate
```

## Command reference

| Command | Purpose |
|---|---|
| `npx tgcloud login` | Link this local project to a Serverless bot |
| `npx tgcloud status` | Compare local state with the cloud |
| `npx tgcloud diff` | Inspect module differences |
| `npx tgcloud push` | Deploy code modules |
| `npx tgcloud migrate` | Review and apply database schema changes |
| `npx tgcloud migrate --dry-run` | Preview migration changes without applying them |
| `npx tgcloud webhook` | Inspect or re-sync platform-managed webhook state |
| `npx tgcloud fetch` | Fetch cloud state for inspection |
| `npx tgcloud pull` | Sync local files from cloud state |
| `npx tgcloud run handlers/message '{...}'` | Run a handler with a test payload without deploying |

Use `npx tgcloud --help` if a command or option differs in the installed CLI version.

## Troubleshooting

- **No CLI access token found:** confirm you are in this repository, then run `npx tgcloud login` using the CLI Access token.
- **Bot does not respond:** check `npx tgcloud status`, `npx tgcloud webhook`, and whether `tgcloud/handlers/message.js` was deployed.
- **Module import errors:** use relative imports with `.js` extensions and import platform APIs from `sdk` / `sdk/db`.
- **Migration prompt appears:** read the proposed schema changes before approving them.
- **Push rejected because cloud is newer:** run `npx tgcloud fetch`, inspect differences, and sync intentionally before retrying.
- **Moderation permission errors:** promote the bot and grant the specific Telegram permission required.
- **Termux mirror errors:** use `termux-change-repo` to select a working mirror, then `pkg update`.

## Security checklist

- Keep `.tgcloud/` out of Git.
- Never commit CLI Access or Bot API tokens.
- Do not use `git clean -fdx` or `git reset --hard` as generic fixes; they can remove local work and ignored state.
- Review code and migrations before deploying to a live bot.
- Test group moderation in a private test group first.
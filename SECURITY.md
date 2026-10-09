# Security Policy

## Reporting a vulnerability

Please do not publish access tokens, private chat data, or exploitable details in a public issue. Open a private report through GitHub's Security tab if private vulnerability reporting is enabled for this repository. Otherwise, contact the maintainer privately.

## Token safety

- Never commit a Telegram Bot API token or Telegram Serverless CLI Access token.
- The `.tgcloud/` directory is local state and must remain ignored by Git.
- Do not paste tokens into issue reports, screenshots, chat messages, shell commands that enter history, or source files.
- If a token is exposed, revoke or rotate it using the appropriate BotFather controls.

## Group safety

- Test changes in a private test group before deploying to a live community.
- Grant the bot only the administrator permissions needed for enabled features.
- Review code and database migrations before deployment.
- Group moderation commands must only be used by authorized group administrators.

## Scope

This policy covers the source code and documentation in this repository. Telegram platform behavior, availability and API limits are controlled by Telegram.
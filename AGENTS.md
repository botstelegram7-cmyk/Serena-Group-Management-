# AGENTS.md — Serena Group Manager

## Project overview

This is a single Telegram Serverless bot project. The deployed runtime modules live under `tgcloud/`; local documentation, package metadata and CLI credentials are not runtime modules.

## Runtime conventions

- Use the platform SDK from `sdk` and `sdk/db`; do not assume arbitrary npm dependencies are available inside the runtime.
- Use relative imports with explicit `.js` extensions for project modules.
- Keep handlers as default-exported async functions.
- Treat Telegram update payloads as untrusted input. Escape user-controlled values before inserting them into HTML-formatted messages.
- Await SDK/database operations and handle expected Telegram API errors.
- Do not rely on filesystem access, long-running processes, timers or module globals for durable state.
- Store persistent group settings and warning history in the built-in database.
- Keep moderation commands restricted to authorized group administrators and do not target other administrators or the group owner.
- Never log, commit or expose CLI Access tokens or Bot API tokens.

## Files

- `tgcloud/handlers/message.js` — command routing, member moderation, rules, warnings, purge, pin and welcome handling.
- `tgcloud/handlers/callback_query.js` — inline keyboard callback routing.
- `tgcloud/lib/ui.js` — menu keyboards, help text and HTML-safe display helpers.
- `tgcloud/schema.js` — persistent database schema.
- `docs/tgcloud-sdk.md` — platform SDK reference.
- `deployment termux.md` — practical Android deployment guide.

## Change and deploy workflow

1. Review the changed files and run `npm install` if dependencies changed.
2. Validate JavaScript syntax and JSON manifests.
3. Test in a private Telegram group.
4. Run `npx tgcloud status` and `npx tgcloud diff`.
5. Run `npx tgcloud push`.
6. Review migration prompts and run `npx tgcloud migrate` if needed.
7. Verify with `npx tgcloud status` and test the relevant commands.

Never use destructive Git or force-deployment commands as routine troubleshooting.
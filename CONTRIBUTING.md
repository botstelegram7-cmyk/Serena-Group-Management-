# Contributing

Thanks for helping improve Serena Group Manager.

## Before changing code

- Read [README.md](README.md), [AGENTS.md](AGENTS.md), and the relevant sections of [docs/tgcloud-sdk.md](docs/tgcloud-sdk.md).
- Keep runtime code inside supported `tgcloud/` directories.
- Use platform SDK APIs and explicit `.js` extensions for relative imports.
- Escape user-controlled text before sending HTML parse-mode messages.
- Keep moderation actions permission-checked and never target group administrators or owners.
- Never add tokens, private chat content or local `.tgcloud/` state to a commit.

## Testing

Run:

```bash
npm install
node --input-type=module --check < tgcloud/handlers/message.js
node --input-type=module --check < tgcloud/handlers/callback_query.js
node --input-type=module --check < tgcloud/lib/ui.js
node --input-type=module --check < tgcloud/schema.js
npx tgcloud status
npx tgcloud diff
```

Test command flows in a private group before deploying to a live group. Database changes require careful review; code deployment and migrations are separate steps.

## Deploying changes

```bash
npx tgcloud push
npx tgcloud migrate
npx tgcloud status
```

Review migration prompts before confirming. Never use force push/deploy commands unless intentionally replacing a newer remote state and after reviewing the impact.

## Pull requests and reports

Describe the user-facing behavior, list changed commands or schema, include test results, and redact all secrets from logs. For security issues, follow [SECURITY.md](SECURITY.md).
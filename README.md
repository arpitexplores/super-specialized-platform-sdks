# Super Specialized Platform SDKs

Build with specialised platforms including Shopify, Salesforce, Telegram bots, ERP/CRM, and messaging SDKs.

## Install

Copy this folder into your agent's skills directory, then restart or reload the agent.

```bash
cp -R super-specialized-platform-sdks ~/.your-agent/skills/
```

Use it by name:

```text
Use $super-specialized-platform-sdks to help with this request.
```

## Best For

- Shopify development
- Salesforce development
- Telegram/Discord-style bots
- ERP/CRM integrations
- platform SDK guidance

## Outputs

- platform implementation plan
- API and auth notes
- data model guidance
- integration checklist
- deployment considerations

## Modules

| Module | Purpose |
| --- | --- |
| `salesforce-development.md` | Salesforce development, integrations, Apex/Flow concepts, CRM data, and platform patterns |
| `shopify-development.md` | Shopify themes, apps, commerce workflows, APIs, storefronts, and checkout considerations |
| `telegram-bot-builder.md` | Telegram bot architecture, messaging workflows, API usage, deployment, and automation patterns |

## Example Prompts

- `Use $super-specialized-platform-sdks to plan a Shopify app.`
- `Use $super-specialized-platform-sdks to review this Salesforce integration.`
- `Use $super-specialized-platform-sdks to build a Telegram bot workflow.`

## Package Contents

- `SKILL.md` is the installable skill entry point.
- `references/modules/` contains detailed workflows loaded only when needed.
- `agents/` contains optional agent metadata where supported.
- `scripts/` and `assets/` are optional helpers when bundled.

## Compatibility

This skill is plain Markdown and is intended to be agent-agnostic. If a bundled helper mentions a specific tool path, translate that instruction to the equivalent path for your environment.

## Version

See `VERSION` and `CHANGELOG.md`.

## Licence

MIT. See the root repository `LICENSE`.

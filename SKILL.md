---
name: super-specialized-platform-sdks
description: "Specialized platform SDKs: ERP/CRM, commerce platforms, and messaging/bot platforms."
---

# Super Specialized Platform SDKs

## Overview
Build integrations with specific platforms using their SDKs and best practices.


## User Intent Examples
- "Need help with Salesforce Development for my product/site."
- "Create a plan for Shopify Development."
- "Audit or improve Telegram/Discord Bots."

## Workflow
1. Confirm platform, version, and auth model.
2. Map required API endpoints and permissions.
3. Design data models and sync strategy.
4. Implement SDK integration and error handling.
5. Test with sandbox data and validate limits.
6. Document setup and maintenance.

## Minimal Intake Questions
- Primary goal or outcome
- Scope (pages, systems, teams, or timeframe)
- Constraints (tools, budget, timeline)

## Output Format
- Integration plan and endpoints
- Auth and permission checklist
- Sync and retry strategy
- Testing and validation steps

## Routing Map (Modules)
- **Salesforce Development** -> `references/modules/salesforce-development.md`
- **Shopify Development** -> `references/modules/shopify-development.md`
- **Telegram/Discord Bots** -> `references/modules/telegram-bot-builder.md`

## Bundled References
- `references/modules/`
- `scripts/`
- `assets/`
- `agents/`

## Compatibility Notes
- If any module references slash commands or tool-specific paths, translate them into plain-language steps.
- Keep outputs platform-agnostic unless the user specifies a specific tool, stack, or agent.

## Guardrails
- Respect platform rate limits.
- Avoid hardcoding secrets.
- Validate API changes during upgrades.

# ai-dev

AI development tools for Claude Code, packaged as the `ai-dev-tools` plugin.

## Skills

- **prd-creator**: turns a short PRD input template (or rough notes) into a full PRD for a developer or AI agent. Examples: `prd-examples/`.
- **issue-to-ticket**: turns a reporter's issue template (or a pasted report) into an engineer-ready ticket. Examples: `issue-examples/`.

## Install in another repo

In Claude Code:

```
/plugin marketplace add PaulCornell/ai-dev
/plugin install ai-dev-tools@ai-dev
```

To have everyone who works in a repo get the plugin, add this to that repo's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "ai-dev": { "source": { "source": "github", "repo": "PaulCornell/ai-dev" } }
  },
  "enabledPlugins": { "ai-dev-tools@ai-dev": true }
}
```

Skills from the plugin run on their own when a request matches, or directly as `/ai-dev-tools:prd-creator` and `/ai-dev-tools:issue-to-ticket`.

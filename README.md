# Human Made Claude Code Standards

Claude Code plugin containing Human Made coding standards, conventions, and development workflows for WordPress and Altis projects.

## Installation

### 1. Add the Marketplace

In your project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "hm-standards": {
      "source": { "source": "github", "repo": "humanmade/claude-code-standards" }
    }
  }
}
```

### 2. Install the Plugin

Run in Claude Code:
```
/plugin install human-made-standards@hm-standards
```

Or add to project settings for automatic prompting:
```json
{
  "extraKnownMarketplaces": {
    "hm-standards": {
      "source": { "source": "github", "repo": "humanmade/claude-code-standards" }
    }
  },
  "enabledPlugins": {
    "human-made-standards@hm-standards": true
  }
}
```

## Included Skills

### Always Relevant
- **hm-coding-philosophy** - Core engineering principles, code quality hierarchy, simplicity guidelines

### Language-Specific (loaded on demand)
- **php-human-made** - PHP/WordPress coding standards, PHPCS HM-Minimum, bootstrap patterns
- **javascript-human-made** - ES6+ conventions, modern JavaScript patterns
- **react-human-made** - Functional components, hooks, PropTypes, WordPress block editor
- **css-scss-human-made** - BEM naming, CSS custom properties, theme.json integration

### Platform-Specific (loaded on demand)
- **altis-development** - Altis DXP local development, architecture, CLI commands
- **vip-development** - WordPress VIP environment, vip-cli, constraints

### Cross-Cutting (loaded on demand)
- **documentation-standards** - Writing prose, instructions and code examples for docs, ADRs and handbook pages

### Utilities
- **run-linters** - Discover and run project linters (PHPCS, PHPStan, ESLint, Stylelint)

## How Skills Load

Skills are loaded on-demand based on context. Claude reads the skill descriptions and loads relevant skills when:

- Writing PHP code → loads `php-human-made`
- Working on React components → loads `react-human-made`
- Running `composer server` commands → loads `altis-development`
- Asked to lint or check code quality → loads `run-linters`
- Writing a README, ADR or handbook page → loads `documentation-standards`

The `hm-coding-philosophy` skill has a broad description and loads more frequently for general code quality guidance.

## Updating

### Manual Update
```
/plugin update human-made-standards@hm-standards
```

### Enable Auto-Updates
In Claude Code, go to `/plugin` → Marketplaces → select `hm-standards` → Enable auto-update

## Project-Level Configuration

For Altis projects, add to your project's `.claude/CLAUDE.md`:

```markdown
# Project: Client Name

Platform: Altis DXP

## Quick Reference
- Local dev: `composer server start`
- Tests: `composer test`
- Build: `npm run build`
```

For VIP projects:

```markdown
# Project: Client Name

Platform: WordPress VIP
App ID: your-app-id

## Quick Reference
- Local dev: `vip dev-env start`
- Production CLI: `vip @your-app.production -- wp`
```

## Contributing

1. Clone this repository
2. Edit skills in `skills/*/SKILL.md`
3. Bump version in `.claude-plugin/plugin.json`
4. Commit and push

Team members can update to get the latest changes.

## Version History

See [CHANGELOG.md](CHANGELOG.md) for version history.

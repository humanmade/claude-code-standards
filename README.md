# Human Made Claude Code Standards

Human Made coding standards, conventions, and development workflows for WordPress and Altis projects, packaged as agent skills.

## Installation

Install with the [skills](https://github.com/vercel-labs/skills) CLI. The command is `add`, not `install`:

```bash
npx skills add humanmade/claude-code-standards
```

The CLI prompts you to choose which skills to install, which agents to install them for, and whether to install to the project or globally.

### Project install

Installs to `./.claude/skills/`, so the skills are committed with the project and shared with the team:

```bash
npx skills add humanmade/claude-code-standards --skill '*' -a claude-code
```

### Global install

Installs to `~/.claude/skills/`, making the skills available in every project:

```bash
npx skills add humanmade/claude-code-standards --skill '*' -a claude-code -g
```

### Selected skills only

List what the repository provides, then install a subset:

```bash
npx skills add humanmade/claude-code-standards --list
npx skills add humanmade/claude-code-standards --skill php-standards --skill run-linters
```

Add `-y` to skip the confirmation prompts, which is useful in CI.

## Included Skills

### Always relevant
- **hm-coding-philosophy** - Core engineering principles, code quality hierarchy, simplicity guidelines

### Language-specific (loaded on demand)
- **php-standards** - PHP/WordPress coding standards, PHPCS HM-Minimum, bootstrap patterns
- **javascript-standards** - ES6+ conventions, modern JavaScript patterns
- **react-standards** - Functional components, hooks, PropTypes, WordPress block editor
- **css-scss-standards** - BEM naming, CSS custom properties, theme.json integration

### Platform-specific (loaded on demand)
- **altis-development** - Altis DXP local development, architecture, CLI commands
- **vip-development** - WordPress VIP environment, vip-cli, constraints

### Cross-cutting (loaded on demand)
- **documentation-standards** - Writing prose, instructions and code examples for docs, ADRs and handbook pages

### Utilities
- **run-linters** - Discover and run project linters (PHPCS, PHPStan, ESLint, Stylelint)

## How skills load

Claude reads the skill descriptions and loads the relevant skill when the context matches:

- Writing PHP code → loads `php-standards`
- Working on React components → loads `react-standards`
- Running `composer server` commands → loads `altis-development`
- Writing a README, ADR or handbook page → loads `documentation-standards`
- Asked to lint or check code quality → loads `run-linters`

`hm-coding-philosophy` has a broad description and loads more often, for general code quality guidance.

## Updating

Update every installed skill:

```bash
npx skills update
```

Update a single skill:

```bash
npx skills update php-standards
```

Use `-g` to update only global skills, or `-p` for project skills.

## Listing and removing

```bash
npx skills list
npx skills remove php-standards
```

## Project-level configuration

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

1. Clone this repository.
2. Edit or add skills in `skills/*/SKILL.md`. Each skill is a directory containing a `SKILL.md` with `name` and `description` frontmatter.
3. Record the change in [CHANGELOG.md](CHANGELOG.md) and bump the version in `.claude-plugin/plugin.json`.
4. Commit and push.

Team members pick up the change with `npx skills update`.

## Version history

See [CHANGELOG.md](CHANGELOG.md).

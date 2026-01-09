---
name: run-linters
description: Discover and run code linters for the current project. Use when asked to lint code, check code quality, run static analysis, or after completing a feature. Detects PHPCS, PHPStan, ESLint, and Stylelint configurations and runs appropriate checks.
---

# Run Project Linters

When asked to run linters, check code quality, or verify changes:

## 1. Discover Available Linters

Check for configuration files to determine which linters are available:

### PHP Linting
- `phpcs.xml` or `phpcs.xml.dist` → PHPCS available
- `phpstan.neon` or `phpstan.neon.dist` → PHPStan available

### JavaScript Linting
- `.eslintrc.*` or `eslint.config.*` → ESLint available

### CSS/SCSS Linting
- `.stylelintrc*` → Stylelint available

## 2. Check for Lint Scripts

Look in `composer.json` and `package.json` for predefined lint commands:

**composer.json scripts:**
```json
{
  "scripts": {
    "lint": "phpcs && phpstan analyse",
    "phpcs": "phpcs",
    "phpstan": "phpstan analyse",
    "fix": "phpcbf"
  }
}
```

**package.json scripts:**
```json
{
  "scripts": {
    "lint": "eslint . && stylelint '**/*.scss'",
    "lint:js": "eslint .",
    "lint:css": "stylelint '**/*.scss'",
    "lint:fix": "eslint . --fix"
  }
}
```

## 3. Run Linters Based on Changed Files

Determine what type of files were changed and run appropriate linters:

### For PHP Changes
```bash
# If composer script exists:
composer run phpcs

# Or directly:
vendor/bin/phpcs path/to/changed/files

# For static analysis:
composer run phpstan
# Or:
vendor/bin/phpstan analyse path/to/changed/files
```

### For JavaScript/TypeScript Changes
```bash
# If npm script exists:
npm run lint:js

# Or directly:
npx eslint path/to/changed/files
```

### For CSS/SCSS Changes
```bash
# If npm script exists:
npm run lint:css

# Or directly:
npx stylelint "path/to/changed/**/*.scss"
```

### For All Changes
```bash
# Run all available linters
composer run lint   # PHP linters
npm run lint        # JS/CSS linters
```

## 4. For Altis Projects

Run linters through the local server:
```bash
# PHP linting in Altis context
composer server cli -- eval-file .claude/lint-runner.php

# Or if PHPCS is available locally:
composer run phpcs
```

## 5. Report and Fix

After running linters:

1. **Report findings** - Summarize errors and warnings by category
2. **Offer auto-fix** - If available, offer to run:
   - `composer run fix` or `vendor/bin/phpcbf` for PHP
   - `npm run lint:fix` or `npx eslint --fix` for JS
   - `npx stylelint --fix` for CSS/SCSS
3. **Manual fixes** - For issues that can't be auto-fixed, explain what needs to change

## Common Issues and Fixes

### PHPCS
- Missing docblocks → Add appropriate PHPDoc comments
- Incorrect spacing → Usually auto-fixable with phpcbf
- Naming conventions → Rename to match standards

### ESLint
- Unused variables → Remove or prefix with underscore
- Missing dependencies in hooks → Add to dependency array
- Prefer const → Change let to const where possible

### Stylelint
- Color format → Convert to preferred format
- Selector specificity → Refactor to reduce nesting
- Unknown properties → Check for typos or vendor prefixes

---
name: css-scss-human-made
description: Human Made CSS and SCSS standards. Apply when writing styles, reviewing CSS/SCSS, or working on theme styling. Covers BEM naming, CSS custom properties, theme.json integration, and Stylelint configuration.
---

# Human Made CSS/SCSS Standards

## Naming Conventions

Use BEM-like naming:
- Block: `.user-card`
- Element: `.user-card__title`
- Modifier: `.user-card--featured`

```scss
.user-card {
    padding: 1rem;

    &__title {
        font-size: 1.25rem;
    }

    &__content {
        margin-top: 0.5rem;
    }

    &--featured {
        border: 2px solid var(--wp--preset--color--primary);
    }
}
```

## CSS Custom Properties

- Use CSS custom properties via `theme.json` tokens where possible
- Reference WordPress preset values: `var(--wp--preset--color--primary)`
- Define component-specific properties at the component level

```scss
.component {
    --component-spacing: 1rem;
    padding: var(--component-spacing);
}
```

## theme.json Integration

For WordPress themes, prefer `theme.json` for:
- Color palettes
- Typography scales
- Spacing presets
- Layout settings

Reference in CSS:
```css
.element {
    color: var(--wp--preset--color--contrast);
    font-size: var(--wp--preset--font-size--medium);
    padding: var(--wp--preset--spacing--40);
}
```

## SCSS Guidelines

- Use nesting sparingly (max 3 levels deep)
- Extract repeated values into variables
- Use mixins for repeated patterns
- Prefer `@use` over `@import` for module loading

## Class Naming

- Use kebab-case: `.my-component-name`
- Avoid IDs for styling
- Avoid overly generic names: prefer `.article-card` over `.card`
- Prefix utility classes if used: `.u-visually-hidden`

## Linting

Projects use Stylelint:
- Config file: `.stylelintrc.js` or `.stylelintrc.json`
- Run with: `npm run lint:css` or `npx stylelint "**/*.scss"`

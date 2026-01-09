---
name: javascript-human-made
description: Human Made JavaScript coding standards. Apply when writing JavaScript or TypeScript, reviewing JS code, or working on frontend features. Covers ES6+ conventions, modern patterns, and ESLint configuration.
---

# Human Made JavaScript Standards

## Modern JavaScript (ES6+)

- Prefer `const` over `let`; never use `var`
- Use arrow functions for callbacks and context binding
- Use destructuring and spread operators
- Prefer functional programming (`.map()`, `.filter()`, `.reduce()`) over imperative loops
- Use template literals for string interpolation
- Use async/await over raw Promises where possible

## Code Conventions

- Trailing commas in multi-line arrays and objects
- Semicolons end every statement
- Avoid Yoda conditions
- One class per file with `export default`
- Use named exports for utilities and helpers

## Examples

### Prefer
```javascript
const { name, email } = user;
const items = data.map( item => item.id );
const filtered = items.filter( id => id > 0 );
```

### Avoid
```javascript
var name = user.name;
var email = user.email;
var items = [];
for ( var i = 0; i < data.length; i++ ) {
    items.push( data[i].id );
}
```

## Module Organization

- Group imports: external dependencies first, then internal modules
- Keep files focused on a single responsibility
- Export types and interfaces alongside implementations

## Linting

Projects use ESLint with WordPress rules:
- Config file: `.eslintrc.js`, `.eslintrc.json`, or `eslint.config.js`
- Run with: `npm run lint` or `npx eslint .`

## WordPress Integration

When working with WordPress block editor or admin:
- Use `@wordpress/*` packages from npm
- Follow WordPress data store patterns for state management
- Use `wp.i18n` functions for internationalization: `__()`, `_x()`, `sprintf()`

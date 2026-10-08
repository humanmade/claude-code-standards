---
name: php-standards
description: Human Made PHP conventions for WordPress — namespaced procedural code, the bootstrap pattern, file naming, type hints, the HM-Minimum PHPCS ruleset, and input/output security. Use when writing or reviewing PHP in a plugin or theme, adding a feature namespace or bootstrap, or when asked whether some PHP follows HM standards.
---

# Human Made PHP Standards

These standards follow the Human Made coding standards (HM-Minimum PHPCS ruleset).

## Architecture

- Prefer namespaced procedural code over unnecessary OOP abstractions
- Use classes only when genuinely modeling objects or when the pattern benefits from encapsulation
- Group code by feature, not by technology (e.g., `Project\Reports` not `Project\CLI`)

## Bootstrap Pattern

Use the bootstrap pattern for feature initialization:

```php
<?php
namespace Project\Feature;

function bootstrap() : void {
    add_action( 'init', __NAMESPACE__ . '\\register' );
    add_filter( 'the_content', __NAMESPACE__ . '\\filter_content' );
}

function register() : void {
    // Implementation
}
```

## File Naming

- Main plugin file: `plugin.php`
- Main theme file: `functions.php`
- Classes: `class-{classname}.php` (lowercase)
- Namespaced functions: `namespace.php` or `{feature}.php`
- Top-level namespace directory: `inc/`

## Code Conventions

- Array syntax: `[]` over `array()`
- Yoda conditions: Not required
- Visibility: Always declare `public`, `protected`, `private`
- Prefer `protected` over `private` for extensibility
- Type hints: Use for internal functions; exercise caution with WordPress callbacks that may pass unexpected types
- Return types: Only declare when function returns exactly one type

## WordPress Security

- Sanitize all input: `sanitize_text_field()`, `absint()`, `wp_kses_post()`, etc.
- Escape all output: `esc_html()`, `esc_attr()`, `esc_url()`, `wp_kses_post()`
- Use nonces for form submissions and AJAX requests
- Check capabilities before performing actions: `current_user_can()`
- Use prepared statements for database queries: `$wpdb->prepare()`

## Linting

Projects use PHPCS with Human Made standards:
- Ruleset: `HM-Minimum` (or project-specific extension)
- Config file: `phpcs.xml` or `phpcs.xml.dist`
- Run with: `composer run phpcs` or `vendor/bin/phpcs`

PHPStan is used for static analysis:
- Level: 5 (typical)
- Config file: `phpstan.neon` or `phpstan.neon.dist`
- Run with: `composer run phpstan` or `vendor/bin/phpstan analyse`

When PHPCS reports violations, read [references/fixing-violations.md](references/fixing-violations.md) for the fix pattern for each sniff.

## Ruleset exclusions

Every `<exclude>` in a project's `phpcs.xml` carries a comment saying why. An exclusion without a reason becomes permanent, because the next person cannot tell a considered trade-off from a rule someone found annoying.

For anything security-related, prefer a per-line `phpcs:ignore` with a reason over a project-wide `<exclude>`. A project-wide exclusion silences the sniff everywhere, including the places where it would have caught a real bug:

```php
// Table names cannot be parameterised through wpdb::prepare().
$table = $wpdb->prefix . 'my_table';
// phpcs:ignore WordPress.DB.PreparedSQL.InterpolatedNotPrepared -- table name is not user input
$rows = $wpdb->get_results( "SELECT * FROM {$table}" );
```

On PHP 8, use humanmade/coding-standards 2.x. Version 1.x pins WPCS 2.x, and PHPCS crashes with a `trim(null)` deprecation in the PHPCompatibility and `WordPress.WP.I18n` sniffs. Excluding `WordPress.WP.I18n` does not stop the crash, because PHPCompatibility fails first.

Projects that use PSR-4 autoloading need one exclusion whatever the team prefers:

```xml
<rule ref="HM">
    <!--
        PSR-4 autoloading uses PascalCase file names; these sniffs
        assume the WordPress hyphenated-lower convention.
    -->
    <exclude name="WordPress.Files.FileName.NotHyphenatedLowercase"/>
    <exclude name="WordPress.Files.FileName.InvalidClassFileName"/>
    <exclude name="HM.Files.ClassFileName.MismatchedName"/>
</rule>
```

Treat any other exclusion as a project decision to argue for in review.

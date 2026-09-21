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

## Ruleset exclusions

Every `<exclude>` in a project's `phpcs.xml` carries a comment saying why. An exclusion without a reason becomes permanent, because the next person cannot tell a considered trade-off from a rule someone found annoying.

For anything security-related, prefer a per-line `phpcs:ignore` with a reason over a project-wide `<exclude>`. A project-wide exclusion silences the sniff everywhere, including the places where it would have caught a real bug:

```php
// Table names cannot be parameterised through wpdb::prepare().
// phpcs:ignore WordPress.DB.PreparedSQL.InterpolatedNotPrepared -- table name is not user input
$rows = $wpdb->get_results( "SELECT * FROM {$wpdb->prefix}my_table" );
```

Two exclusions are genuinely unavoidable rather than a preference:

```xml
<rule ref="HM">
    <!--
        The sniff crashes on PHP 8.x with WPCS 2.x — a trim(null)
        deprecation inside the sniff itself.
    -->
    <exclude name="WordPress.WP.I18n"/>

    <!--
        PSR-4 autoloading uses PascalCase file names; these sniffs
        assume the WordPress hyphenated-lower convention.
    -->
    <exclude name="WordPress.Files.FileName.NotHyphenatedLowercase"/>
    <exclude name="WordPress.Files.FileName.InvalidClassFileName"/>
    <exclude name="HM.Files.ClassFileName.MismatchedName"/>
</rule>
```

Treat anything beyond these as a project decision to argue for in review.

## Fixing violations

Run the fixer first, then work through what remains grouped by sniff name. The same sniff usually fires many times and takes one fix pattern.

### `Generic.Commenting.DocComment.MissingShort`

Every docblock opens with a short description.

```php
// Bad
/**
 * @param string $api_key Service API key.
 */

// Good
/**
 * Create a new API client.
 *
 * @param string $api_key Service API key.
 */
```

### `Squiz.Commenting.FunctionComment.Missing`

Every function and method needs a docblock. A `phpcs:ignore` placed *between* the docblock and the declaration breaks the association, so the sniff reports the docblock as missing. Put the ignore inline on the declaration instead:

```php
// Bad — the ignore separates the docblock from the function
/**
 * Register the hooks.
 */
// phpcs:ignore HM.Functions.NamespacedFunctions.MissingNamespace
function register_hooks(): void {

// Good — ignore on the declaration line
/**
 * Register the hooks.
 */
function register_hooks(): void { // phpcs:ignore HM.Functions.NamespacedFunctions.MissingNamespace
```

### `Squiz.Commenting.FunctionComment.MissingParamTag`

Every parameter needs an `@param` tag.

```php
/**
 * Run the command.
 *
 * @param array $args  Positional arguments.
 * @param array $assoc Associative arguments.
 */
public function run( array $args, array $assoc ): void {
```

### `Generic.Commenting.DocComment.LongNotCapital`

The long description — the second paragraph of a docblock — starts with a capital letter.

```php
// Bad
 * green: at or above target; amber: within ten; red: below that.

// Good
 * Green: at or above target; amber: within ten; red: below that.
```

### `HM.Security.ValidatedSanitizedInput.MissingUnslash`

Unslash `$_GET` and `$_POST` values before sanitising them.

```php
// Bad
$value = sanitize_text_field( $_POST['field'] );

// Good
$value = sanitize_text_field( wp_unslash( $_POST['field'] ?? '' ) );
```

### `HM.Security.ValidatedSanitizedInput.InputNotSanitized`

Match the sanitiser to the data type.

```php
// Integers — absint() satisfies the sniff, an (int) cast does not
$id = absint( $_POST['id'] ?? 0 );

// Text
$label = sanitize_text_field( wp_unslash( $_POST['label'] ?? '' ) );

// Multi-line text
$body = sanitize_textarea_field( wp_unslash( $_POST['body'] ?? '' ) );

// Keys and slugs
$action = sanitize_key( $_POST['action'] ?? '' );
```

Where the receiving function does the validation, say so in the ignore:

```php
// phpcs:ignore HM.Security.ValidatedSanitizedInput.InputNotSanitized -- validated by the importer
$raw = wp_unslash( $_POST['payload'] ?? '' );
```

### `HM.Functions.NamespacedFunctions.MissingNamespace`

Functions live in a namespace. Where an include file deliberately defines a global helper, suppress it on the declaration with a reason:

```php
function my_helper(): void { // phpcs:ignore HM.Functions.NamespacedFunctions.MissingNamespace -- admin include
```

## Renaming methods to snake_case

HM standards require snake_case method names. A rename touches more than the declaration, and missing one of these leaves a fatal error that no linter catches:

1. The declaration — `public function myMethod(` becomes `public function my_method(`
2. Every call site — `->myMethod(`
3. Hook and callback registrations — `[ $this, 'myMethod' ]`
4. Test doubles — Mockery's `shouldReceive( 'myMethod' )`
5. Named arguments at call sites — `make_helper( myParam: 7 )`

Items 3, 4 and 5 pass the method name as a string or a label rather than a symbol, so no static analysis finds them. Grep the whole tree for the old name before you consider a rename done:

```sh
grep -rn 'myMethod' includes/ tests/
```

The same applies to renaming a parameter: update the declaration, every `$this->` reference to it, and any named-argument call sites.

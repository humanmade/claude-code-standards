# Fixing PHPCS violations

Run `vendor/bin/phpcbf` first. It fixes formatting errors automatically, which shortens the list you fix by hand. Then work through what remains grouped by sniff name. The same sniff usually fires many times and takes one fix pattern.

## `Generic.Commenting.DocComment.MissingShort`

Every docblock opens with a short description.

Bad:

```php
/**
 * @param string $api_key Service API key.
 */
```

Good:

```php
/**
 * Create a new API client.
 *
 * @param string $api_key Service API key.
 */
```

## `Squiz.Commenting.FunctionComment.Missing`

Every function and method needs a docblock. A `phpcs:ignore` placed *between* the docblock and the declaration breaks the association, so the sniff reports the docblock as missing. Put the ignore inline on the declaration instead:

Bad — the ignore separates the docblock from the function:

```php
/**
 * Register the hooks.
 */
// phpcs:ignore HM.Functions.NamespacedFunctions.MissingNamespace
function register_hooks(): void {
```

Good — ignore on the declaration line:

```php
/**
 * Register the hooks.
 */
function register_hooks(): void { // phpcs:ignore HM.Functions.NamespacedFunctions.MissingNamespace
```

## `Squiz.Commenting.FunctionComment.MissingParamTag`

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

## `Generic.Commenting.DocComment.LongNotCapital`

The long description — the second paragraph of a docblock — starts with a capital letter.

Bad:

```php
 * green: at or above target; amber: within ten; red: below that.
```

Good:

```php
 * Green: at or above target; amber: within ten; red: below that.
```

## `HM.Security.ValidatedSanitizedInput.MissingUnslash`

Unslash `$_GET` and `$_POST` values before sanitising them. WordPress adds slashes to request data, so a value sanitised without unslashing is saved with backslashes in it.

Bad:

```php
$value = sanitize_text_field( $_POST['field'] );
```

Good:

```php
$value = sanitize_text_field( wp_unslash( $_POST['field'] ?? '' ) );
```

## `HM.Security.ValidatedSanitizedInput.InputNotSanitized`

Match the sanitiser to the data type.

```php
// Integers — absint() satisfies the sniff, an (int) cast does not,
// because the sniff only recognises sanitising functions
$id = absint( $_POST['id'] ?? 0 );

// Text
$label = sanitize_text_field( wp_unslash( $_POST['label'] ?? '' ) );

// Multi-line text
$body = sanitize_textarea_field( wp_unslash( $_POST['body'] ?? '' ) );

// Keys and slugs
$action = sanitize_key( $_POST['action'] ?? '' );
```

Where the receiving function does the validation, say so in the ignore. Without a reason, a reviewer cannot tell a deliberate choice from a missed sanitiser:

```php
// phpcs:ignore HM.Security.ValidatedSanitizedInput.InputNotSanitized -- validated by the importer
$raw = wp_unslash( $_POST['payload'] ?? '' );
```

## `HM.Functions.NamespacedFunctions.MissingNamespace`

Functions live in a namespace, because a global function name can collide with another plugin's and cause a fatal error. Where an include file deliberately defines a global helper, suppress it on the declaration with a reason:

```php
function my_helper(): void { // phpcs:ignore HM.Functions.NamespacedFunctions.MissingNamespace -- admin include
```

## Renaming methods to snake_case

HM standards require snake_case method names. A rename touches more than the declaration, and missing one of these leaves a fatal error that PHPCS does not catch:

1. The declaration — `public function myMethod(` becomes `public function my_method(`
2. Every call site — `->myMethod(`
3. Hook and callback registrations — `[ $this, 'myMethod' ]`
4. Test doubles — Mockery's `shouldReceive( 'myMethod' )`

Items 3 and 4 pass the method name as a string rather than a symbol. PHPStan reports a stale hook callback only at level 9 with WordPress stubs, and does not report a stale Mockery expectation at all. Grep the whole tree for the old name before you consider a rename done:

```sh
grep -rn 'myMethod' includes/ tests/
```

Renaming a parameter breaks every call that passes it by name, such as `make_helper( myParam: 7 )`. PHP throws an "Unknown named parameter" error when that call runs. PHPStan reports these calls, so run it after the rename. Update the declaration, its uses in the function body, and every named-argument call site.

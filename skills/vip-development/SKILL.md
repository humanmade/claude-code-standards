---
name: vip-development
description: WordPress VIP projects — vip-cli commands, client-mu-plugins architecture, and VIP platform constraints. Use when a project has a VIP structure, or when asked to run vip-cli, reach WP-CLI or the database on a VIP application, tail logs, work with the local dev-env, or check whether something is permitted on VIP.
---

# WordPress VIP Development

WordPress VIP is an enterprise WordPress hosting platform with specific architecture requirements and tooling.

## Project Structure

```
project/
├── client-mu-plugins/       # Client must-use plugins
│   ├── plugin-name/
│   │   ├── plugin.php
│   │   └── inc/
│   └── plugin-loader.php    # Loads client mu-plugins
├── plugins/                 # Standard plugins
├── themes/
│   └── theme-name/
│       ├── functions.php
│       ├── inc/
│       └── theme.json
├── vip-config/
│   └── vip-config.php       # VIP-specific configuration
├── images/                  # Imported media (optional)
├── languages/               # Translation files
└── composer.json
```

## VIP CLI Commands

VIP uses `vip` CLI (install via npm: `npm install -g @automattic/vip`):

### Application Management
```bash
vip app list                           # List your VIP applications
vip app --app=<app-id>                 # Select application context
```

### WP-CLI Access
```bash
vip @<app>.<env> -- wp <command>

# Examples:
vip @mysite.production -- wp post list --post_type=page
vip @mysite.develop -- wp user list
vip @mysite.staging -- wp option get siteurl
vip @mysite.develop -- wp cache flush
```

### Database Access
```bash
vip @<app>.<env> db                    # Interactive database shell
```

### Logs
```bash
vip @<app>.<env> logs                  # Application logs
vip @<app>.<env> logs --type=batch     # Batch/cron logs
```

### Dev Environment (Local)
```bash
vip dev-env create                     # Create local environment
vip dev-env start                      # Start local environment
vip dev-env stop                       # Stop local environment
vip dev-env destroy                    # Remove local environment

# WP-CLI in local dev-env
vip dev-env exec -- wp <command>
```

## client-mu-plugins Architecture

Client code lives in `client-mu-plugins/`:

```php
<?php
// client-mu-plugins/my-feature/plugin.php
namespace Project\MyFeature;

function bootstrap() : void {
    add_action( 'init', __NAMESPACE__ . '\\register' );
}
```

Load via `plugin-loader.php`:
```php
<?php
// client-mu-plugins/plugin-loader.php
require_once __DIR__ . '/my-feature/plugin.php';
Project\MyFeature\bootstrap();
```

## VIP-Specific Constraints

### Forbidden Functions
VIP restricts certain functions for security and performance:
- No `eval()`, `create_function()`
- No direct file writes outside uploads: use VIP File System API
- No `wp_remote_get()` to localhost
- Limited use of `set_time_limit()`

### Required Practices
- Use `wpcom_vip_file_get_contents()` for remote requests with caching
- Use `wpcom_vip_attachment_url_to_postid()` instead of `attachment_url_to_postid()`
- Avoid uncached queries: use VIP's caching layer

### Environment Variables
Access via:
```php
$value = defined( 'MY_CONSTANT' ) ? MY_CONSTANT : getenv( 'MY_CONSTANT' );
```

## Code Analysis

VIP provides code analysis tools:
```bash
# Run VIP code analysis locally
vip @<app> code-analysis

# Or use the PHPCS VIP ruleset
vendor/bin/phpcs --standard=WordPress-VIP-Go
```

## Deployment

VIP deploys from git branches:
- `develop` branch → develop environment
- `preprod` branch → staging environment
- `master`/`main` branch → production environment

Deployments are triggered by git push and go through VIP's review process.

## Asset Handling

Similar to standard WordPress, but note:
- Use `wp_enqueue_*` functions exclusively
- CDN is handled automatically by VIP
- Use versioning via asset manifest files

```php
function enqueue_assets() : void {
    $manifest = json_decode(
        file_get_contents( get_template_directory() . '/build/manifest.json' ),
        true
    );

    wp_enqueue_script(
        'theme-scripts',
        get_template_directory_uri() . '/build/' . $manifest['main.js'],
        [],
        null,
        true
    );
}
add_action( 'wp_enqueue_scripts', __NAMESPACE__ . '\\enqueue_assets' );
```

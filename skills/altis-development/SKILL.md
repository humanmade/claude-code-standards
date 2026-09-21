---
name: altis-development
description: Altis DXP projects — local server commands, mu-plugins architecture, asset handling and deploys. Use when a project has an Altis config or `composer server` scripts, or when asked to start or stop the local server, run WP-CLI, open a database shell, tail logs, or run a PHP script in WordPress context on Altis.
---

# Altis DXP Development

Altis is an enterprise WordPress platform by Human Made. Projects use a specific architecture and tooling.

## Project Structure

```
project/
├── .config/                 # Altis configuration
│   └── environments/        # Environment-specific config
├── content/
│   ├── mu-plugins/          # Must-use plugins (auto-loaded)
│   │   ├── plugin-name/
│   │   │   ├── plugin.php   # Main plugin file
│   │   │   └── inc/         # Namespaced code
│   │   └── loader.php       # Loads all mu-plugins
│   ├── plugins/             # Standard plugins
│   └── themes/
│       └── theme-name/
│           ├── functions.php
│           ├── inc/
│           └── theme.json
├── composer.json
├── package.json
└── webpack.config.js
```

## mu-plugins Architecture

Must-use plugins in `content/mu-plugins/` are auto-loaded via `loader.php`:

```php
<?php
// content/mu-plugins/my-feature/plugin.php
namespace Project\MyFeature;

function bootstrap() : void {
    add_action( 'init', __NAMESPACE__ . '\\register' );
}

// Called from loader.php
bootstrap();
```

The loader typically includes each plugin's main file which calls its bootstrap function.

## Local Development Commands

Altis uses `composer server` for local development:

### Server Management
```bash
composer server start      # Start local environment
composer server stop       # Stop local environment
composer server restart    # Restart services
composer server status     # Check service status
composer server destroy    # Remove local environment completely
```

### WP-CLI Access
```bash
composer server cli -- <command>

# Examples:
composer server cli -- post list --post_type=page
composer server cli -- user list
composer server cli -- option get siteurl
composer server cli -- cache flush
```

### Database Access
```bash
composer server db info    # Get connection details
composer server db         # Connect to MySQL directly
composer server db export  # Export database
composer server db import dump.sql  # Import database
```

### Logs
```bash
composer server logs       # All logs
composer server logs php   # PHP/WordPress logs
composer server logs nginx # Web server logs
```

### Running PHP Scripts
```bash
# Execute a PHP file in WordPress context
composer server cli -- eval-file path/to/script.php

# Execute inline PHP
composer server cli -- eval "echo get_option('siteurl');"
```

## Asset Handling

Assets are typically built with webpack:

```bash
npm install          # Install dependencies
npm run build        # Production build
npm run start        # Development with watch
```

Enqueue in PHP:
```php
function enqueue_assets() : void {
    $asset_file = include get_template_directory() . '/build/index.asset.php';

    wp_enqueue_script(
        'theme-scripts',
        get_template_directory_uri() . '/build/index.js',
        $asset_file['dependencies'],
        $asset_file['version'],
        true
    );
}
add_action( 'wp_enqueue_scripts', __NAMESPACE__ . '\\enqueue_assets' );
```

## Elasticsearch

Altis includes Elasticsearch for search:

```bash
composer server cli -- elasticpress index --setup   # Initial index
composer server cli -- elasticpress index           # Re-index content
```

## Redis Object Cache

Object caching is handled automatically. Clear with:
```bash
composer server cli -- cache flush
```

## Environment Configuration

Environment-specific settings in `.config/`:

```php
// .config/environments/local.php
<?php
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );
define( 'SCRIPT_DEBUG', true );
```

## Fetching Pages for Debugging

```bash
# Static HTML (no JS execution)
curl -s https://project.altis.dev/page-slug | head -100

# With authentication cookie
curl -s -b "wordpress_logged_in_xxx=value" https://project.altis.dev/wp-admin/
```

For JavaScript-rendered content, use browser dev tools or Playwright.

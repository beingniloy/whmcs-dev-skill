---
name: whmcs-dev-skill
description: >
  Advanced Senior WHMCS Developer & Architect skill for AI coding agents. Builds,
  debugs, maintains, and TESTS WHMCS Addon Modules, Provisioning (Server) Modules,
  Domain Registrar Modules, Payment Gateway Modules, and Action Hooks. Enforces
  WHMCS 8.x / 9.x best practices, modern PHP 8.1+/8.2+ standards, Laravel Capsule
  ORM, Smarty v3/v4 templating, PSR-1/PSR-2/PSR-12 coding style, comprehensive
  testing, performance optimization, security hardening, and production-ready
  deployment patterns.
license: MIT
compatibility: >
  Works with all AI coding agents including Claude Code, GitHub Copilot,
  Cursor, Windsurf, VS Code, Amp, Goose, and OpenCode. Requires PHP 8.1+
  for WHMCS 8.x (PHP 8.2+ mandatory for WHMCS 9.x) for generated code.
metadata:
  author: Niloy
  version: "1.0.0"
  website: "https://niloy.io"
---

# WHMCS Dev Skill — Advanced AI Agent Skill

> **Scope**: Full-stack WHMCS module development covering Addon Modules,
> Provisioning (Server) Modules, Domain Registrar Modules, Payment Gateway
> Modules (Third-Party, Merchant, Tokenised), Action Hooks, Internal/External
> API integration, Theme/Template customisation, **Testing**, **Performance
> Optimization**, **Security Hardening**, **Production Deployment**, and
> **Internationalization**.

---

## Table of Contents

1. [Operational Boundaries](#1-operational-boundaries)
2. [Platform Requirements & WHMCS 9.x Migration](#2-platform-requirements--whmcs-9x-migration)
3. [Coding Standards & PSR-12](#3-coding-standards--psr-12)
4. [Database Operations](#4-database-operations)
5. [Module Development](#5-module-development)
   - 5.1 [Addon Modules](#51-addon-modules)
   - 5.2 [Provisioning (Server) Modules](#52-provisioning-server-modules)
   - 5.3 [Domain Registrar Modules](#53-domain-registrar-modules)
   - 5.4 [Payment Gateway Modules](#54-payment-gateway-modules)
6. [Action Hooks](#6-action-hooks)
7. [API Integration](#7-api-integration)
8. [Templating & UI](#8-templating--ui)
9. [Security Hardening & Best Practices](#9-security-hardening--best-practices)
10. [Testing Strategy](#10-testing-strategy)
11. [Performance Optimization](#11-performance-optimization)
12. [Error Handling & Logging](#12-error-handling--logging)
13. [Module Upgrade Pattern](#13-module-upgrade-pattern)
14. [Common Pitfalls & Anti-Patterns](#14-common-pitfalls--anti-patterns)
15. [Project Structure Templates](#15-project-structure-templates)
16. [Quick-Reference Code Snippets](#16-quick-reference-code-snippets)
17. [Development Environment Setup](#17-development-environment-setup)
18. [Code Quality & Review Guidelines](#18-code-quality--review-guidelines)
19. [Internationalization (i18n)](#19-internationalization-i18n)
20. [Deployment & CI/CD](#20-deployment--cicd)
21. [Real-World Examples](#21-real-world-examples)
22. [Official References](#22-official-references)

---

## 1. Operational Boundaries

### ✅ ALWAYS

- Add `defined("WHMCS") or die("Access Denied");` as the **first line** of
  every PHP file.
- Use `Illuminate\Database\Capsule\Manager` (Laravel Capsule) for **all**
  database operations.
- Use `logModuleCall()` for **every** external API request to enable the
  WHMCS Module Log (Configuration → System Logs → Module Log).
- Use `logActivity()` to write meaningful entries to the System Activity Log.
- Use Smarty `.tpl` template files for **all** HTML output — never echo raw
  HTML in logic files.
- Follow PSR-1, PSR-2, and PSR-12 coding standards.
- Use `<?php` full opening tags; omit the closing `?>` tag in pure-PHP files.
- Wrap all external API calls and database schema changes in `try/catch`
  blocks.
- Use parameter binding (Capsule / PDO) — **never** concatenate user input
  into SQL.
- Validate and sanitise **all** `$_POST` and `$_GET` input using
  `filter_input()` or `htmlspecialchars()`.
- Prefix custom database tables with `mod_` (e.g., `mod_yourmodule_data`).
- Provide a `lang/english.php` language file for every module.
- Run unit/integration tests before committing module changes.
- Write code compatible with **PHP 8.1+** (prefer 8.2 / 8.3) with strict
  type hints.
- Test in a **staging environment** before deploying to production.
- Use **return type declarations** for all functions.
- Use **property type declarations** for all class properties (PHP 8.2+).

### ⚠️ ASK FIRST

- Before performing bulk refunds or mass invoice operations.
- Before performing `DROP TABLE` operations in deactivation functions.
- Before changing a client's password or authentication settings.
- Before modifying any server-level configuration.
- Before deleting or merging client accounts.
- Before performing any action that cannot be undone.

### 🚫 NEVER

- Modify WHMCS core files (`/admin/`, `/includes/`, `/vendor/`). Use Hooks
  or Modules instead.
- Modify `configuration.php` directly.
- Use `mysql_*`, `mysqli_*`, or raw PDO — always use Capsule.
- Use deprecated `{php}` tags in Smarty templates (**removed in Smarty v4**).
- Use `$_REQUEST` — be explicit with `$_POST` or `$_GET`.
- Hardcode absolute file paths — use `ROOTDIR`, `$CONFIG['SystemURL']`, or
  WHMCS constants.
- Store sensitive data (passwords, API keys) in plain text — use WHMCS's
  `encrypt()` / `decrypt()` helpers.
- Output detailed error messages to end-users in production — log them
  internally instead.
- Use `echo` or `print` for output in module files — return structured arrays
  or use Smarty templates.
- Use **dynamic properties** on classes (deprecated in PHP 8.2, error in 9.0).
- Ignore **type strictness** — always declare types.
- Skip **error handling** — every external call must have try/catch.
- Leave **debug code** in production — remove all `var_dump()`, `print_r()`,
  error_log() calls.

---

## 2. Platform Requirements & WHMCS 9.x Migration

### Version Matrix

| Component          | WHMCS 8.x (8.11+)       | WHMCS 9.x               |
|--------------------|--------------------------|--------------------------|
| **PHP**            | 8.1 min, 8.2 recommended | 8.2 min, 8.3 recommended |
| **ionCube Loader** | 13.0.2+                  | 13.0.2+ (14.4.0 rec.)   |
| **Smarty**         | v3.1.x                   | v4.3.4                   |
| **GuzzleHTTP**     | v7.4                     | v7.4.5                   |
| **Illuminate**     | v7.x                     | v9.0                     |
| **MySQL/MariaDB**  | 5.7+ / 10.2+             | 8.0+ / 10.6+            |

### Key WHMCS 9.x Breaking Changes & Migration Guide

#### Smarty v3 → v4 Migration

**Breaking Changes:**

- Legacy Smarty tags (`{php}`, `{section}`, `{ldelim}`/`{rdelim}` syntax)
  are **removed**.
- Smarty Template Objects are **no longer supported**.
- All templates must use Smarty v4 syntax.

**Migration Checklist:**

```php
// ❌ OLD (Smarty v3) — WILL BREAK
{php} echo $variable; {/php}
{section name=foo loop=$items}...{/section}

// ✅ NEW (Smarty v4) — REQUIRED
{$variable}
{foreach from=$items item=item}...{/foreach}
```

**Template Variables:** Use `{foreach}` instead of `{section}`:

```smarty
{* ❌ OLD *}
{section name=i loop=$items}
  {$items[i].name}
{/section}

{* ✅ NEW *}
{foreach from=$items item=item}
  {$item.name}
{/foreach}
```

#### Illuminate v7 → v9 Migration

**Breaking Changes:**

- Database relation methods and Eloquent patterns from v7 may cause **fatal
  errors**.
- `Capsule::table()->first()` behavior unchanged, but some collection
  methods changed.

**Migration Steps:**

1. Test **all** Capsule queries against Illuminate v9
2. Replace any deprecated collection methods
3. Verify `get()`, `first()`, `toArray()` behavior
4. Check schema builder compatibility

**Example:**

```php
// ✅ SAFE — Works in both v7 and v9
$results = Capsule::table('tblclients')
    ->where('status', '=', 'Active')
    ->get()
    ->toArray();

// ⚠️ TEST CAREFULLY — May behave differently
$results = Capsule::table('tblclients')
    ->where('status', '=', 'Active')
    ->chunk(100, function ($results) {
        // Process in chunks
    });
```

#### PHP 8.2+ Dynamic Properties

**Deprecated:** Dynamic properties on classes are deprecated in PHP 8.2
and will cause errors in PHP 9.0.

**Fix:**

```php
// ❌ OLD — Will break
class MyClass {
    public $dynamicProperty; // Not declared
}

// ✅ NEW — Required
class MyClass {
    public string $declaredProperty;
    public ?string $optionalProperty = null;
}
```

#### `localAPI()` Behavior Changes

```php
// WHMCS 8.x — Returns array with 'result' key
$results = localAPI('GetClientsDetails', ['clientid' => 1]);

// WHMCS 9.x — Same structure, but some fields may be missing
// Always check for key existence
$clientName = $results['fullname'] ?? 'Unknown';
```

#### Invoice Immutability & Credit Notes Compliance (WHMCS 9.0+)

**Breaking Changes:**
In WHMCS 9.0, invoices that are published (sent to client/out of draft) are **immutable**. They cannot be modified directly via database queries or updates. Any adjustments, write-offs, or refunds must be issued via **Credit/Debit Notes** to maintain audit trails.

**Rules for AI Agents:**
- **Never** perform raw `update` queries using Capsule on `tblinvoices` or `tblinvoiceitems` to change amounts on published invoices.
- Use the WHMCS Credit Note system or the `ApplyCredit` API/Internal routines to apply adjustments.

#### Buy Flow API Extensibility (WHMCS 9.0+)

**Overview:**
WHMCS 9.0 introduced the Buy Flow API built on OpenAPI and JSON:API standards to power the new AJAX-based Nexus shopping cart.

**Implementation:**
- When customizing checkout workflows, utilize the Buy Flow API endpoints instead of custom session-hacking or manual cart table updates.
- Keep cart endpoints responsive and conform to the JSON:API specification.

---

## 3. Coding Standards & PSR-12

Based on the [WHMCS Style Guide](https://developers.whmcs.com/modules/style-guide/)
and [PSR-12](https://www.php-fig.org/psr/psr-12/):

### File Structure

```
✓  Use <?php ?> full tags only (never <? ?>).
✓  Omit closing ?> in pure-PHP files.
✓  Indent with 4 spaces (no tabs).
✓  No trailing whitespace on lines.
✓  Use Unix line endings (\n), not Windows (\r\n).
✓  End every file with a single newline.
✓  UTF-8 encoding without BOM.
✓  Follow PSR-1 (Basic Coding Standard), PSR-2 (Coding Style), and PSR-12 (Extended Coding Style).
✓  Use strict_types declaration: declare(strict_types=1);
✓  Use PHP 8.1+ type hints for all parameters, return types, and properties.
✓  Use readonly properties where applicable (PHP 8.1+).
✓  Use enum types where appropriate (PHP 8.1+).
```

### Naming Conventions

| Element             | Convention                     | Example                          |
|---------------------|--------------------------------|----------------------------------|
| Module Directory    | lowercase, letters & numbers   | `mymodule` or `my_module`        |
| Module Functions    | `{modulename}_FunctionName`    | `mymodule_config()`              |
| Hook Functions      | Unique prefixed name           | `mymodule_hookClientAdd()`       |
| Database Tables     | `mod_{modulename}_{entity}`    | `mod_mymodule_settings`          |
| Config Fields       | camelCase keys                 | `apiKey`, `secretToken`          |
| Template Files      | lowercase with hyphens         | `admin-dashboard.tpl`            |
| Language Keys       | snake_case                     | `module_description`             |
| Classes             | PascalCase                     | `ApiClient`, `DataHandler`       |
| Constants           | UPPER_SNAKE_CASE               | `MAX_RETRY_ATTEMPTS`             |
| Private Methods     | camelCase with underscore      | `_validateInput()`               |

### Type Declarations

```php
<?php

declare(strict_types=1);

// ✅ REQUIRED — All functions must have type declarations
function mymodule_processData(
    string $input,
    int $userId,
    bool $isAdmin = false
): array {
    // Function body
}

// ✅ REQUIRED — All class properties must be declared
class MyModule {
    public string $name;
    public int $version;
    public ?string $description = null;
    private readonly array $config;
    
    public function __construct(string $name, int $version) {
        $this->name = $name;
        $this->version = $version;
    }
}

// ✅ REQUIRED — Use union types (PHP 8.0+)
function getModuleStatus(int $moduleId): string|false {
    // Returns string or false
}

// ✅ REQUIRED — Use intersection types (PHP 8.1+)
function processData(): Countable&Iterator {
    // Returns object implementing both interfaces
}
```

---

## 4. Database Operations

### Modern Pattern — Laravel Capsule

```php
<?php

use Illuminate\Database\Capsule\Manager as Capsule;

// SELECT — Basic
$clients = Capsule::table('tblclients')
    ->where('status', '=', 'Active')
    ->orderBy('lastname', 'asc')
    ->get();

// SELECT — Single row
$client = Capsule::table('tblclients')
    ->where('id', '=', $clientId)
    ->first();

// SELECT — With column selection (avoid SELECT *)
$client = Capsule::table('tblclients')
    ->select('id', 'firstname', 'lastname', 'email')
    ->where('id', '=', $clientId)
    ->first();

// SELECT — With joins
$services = Capsule::table('tblhosting')
    ->join('tblclients', 'tblhosting.userid', '=', 'tblclients.id')
    ->select('tblhosting.*', 'tblclients.firstname', 'tblclients.lastname')
    ->where('tblhosting.status', '=', 'Active')
    ->get();

// INSERT
$moduleId = Capsule::table('mod_mymodule_logs')->insertGetId([
    'client_id' => $clientId,
    'action'    => 'provisioned',
    'created_at' => date('Y-m-d H:i:s'),
]);

// UPDATE — Single row
Capsule::table('tblhosting')
    ->where('id', '=', $serviceId)
    ->update(['notes' => 'Updated via module']);

// UPDATE — Batch
Capsule::table('tblhosting')
    ->where('status', '=', 'Pending')
    ->where('created_at', '<', date('Y-m-d H:i:s', strtotime('-7 days')))
    ->update(['status' => 'Cancelled']);

// DELETE (use with caution)
Capsule::table('mod_mymodule_logs')
    ->where('created_at', '<', $cutoffDate)
    ->delete();

// UPSERT / UPDATE-OR-INSERT — updateOrInsert works on both WHMCS 8.x and 9.x
Capsule::table('mod_mymodule_cache')->updateOrInsert(
    ['key' => 'user_123'], // Unique identifier to check existence
    ['value' => 'cached_data', 'expires_at' => date('Y-m-d H:i:s', strtotime('+1 hour'))] // Values to set/update
);
```

### Schema Creation (in `_activate`)

```php
<?php

use Illuminate\Database\Capsule\Manager as Capsule;

function mymodule_activate(): array
{
    try {
        Capsule::schema()->create('mod_mymodule_data', function ($table) {
            /** @var \Illuminate\Database\Schema\Blueprint $table */
            $table->increments('id');
            $table->unsignedInteger('client_id');
            $table->string('key', 255);
            $table->text('value')->nullable();
            $table->timestamps();

            // Indexes for performance
            $table->index('client_id');
            $table->index('key');
            $table->unique(['client_id', 'key']);
        });

        // Create additional audit table
        Capsule::schema()->create('mod_mymodule_audit', function ($table) {
            $table->increments('id');
            $table->unsignedInteger('module_data_id');
            $table->string('action', 50);
            $table->text('details')->nullable();
            $table->timestamps();

            $table->index('module_data_id');
            $table->index('action');
        });

        return [
            'status'      => 'success',
            'description' => 'Module activated successfully. Configure settings above.',
        ];
    } catch (\Exception $e) {
        return [
            'status'      => 'error',
            'description' => 'Failed to create database table: ' . $e->getMessage(),
        ];
    }
}
```

### Schema Updates (in `_upgrade`) with Transactions

```php
<?php

use Illuminate\Database\Capsule\Manager as Capsule;

function mymodule_upgrade(array $vars): void
{
    $currentVersion = $vars['version'];

    // Version 1.1.0: Add status column
    if (version_compare($currentVersion, '1.1.0', '<')) {
        try {
            // Use transaction for safe schema changes
            Capsule::connection()->beginTransaction();
            
            Capsule::schema()->table('mod_mymodule_data', function ($table) {
                $table->string('status', 50)->default('active')->after('value');
            });
            
            // Verify the column was added
            if (!Capsule::schema()->hasColumn('mod_mymodule_data', 'status')) {
                throw new \Exception('Column "status" was not added successfully');
            }
            
            Capsule::connection()->commit();
            logActivity("MyModule: Upgraded to v1.1.0 — added status column.");
        } catch (\Exception $e) {
            Capsule::connection()->rollBack();
            logActivity("MyModule: Upgrade to v1.1.0 failed — " . $e->getMessage());
            throw $e; // Re-throw to prevent partial upgrades
        }
    }

    // Version 1.2.0: Add index and new table
    if (version_compare($currentVersion, '1.2.0', '<')) {
        try {
            Capsule::connection()->beginTransaction();
            
            Capsule::schema()->table('mod_mymodule_data', function ($table) {
                $table->index('status');
            });

            if (!Capsule::schema()->hasTable('mod_mymodule_audit')) {
                Capsule::schema()->create('mod_mymodule_audit', function ($table) {
                    $table->increments('id');
                    $table->unsignedInteger('record_id');
                    $table->string('action', 100);
                    $table->text('details')->nullable();
                    $table->timestamp('created_at')->useCurrent();
                });
            }
            
            Capsule::connection()->commit();
            logActivity("MyModule: Upgraded to v1.2.0 — added audit table.");
        } catch (\Exception $e) {
            Capsule::connection()->rollBack();
            logActivity("MyModule: Upgrade to v1.2.0 failed — " . $e->getMessage());
            throw $e;
        }
    }
}
```

### 🚫 Forbidden Pattern — Raw SQL

```php
// ❌ NEVER DO THIS
$result = mysql_query("SELECT * FROM tblclients WHERE id = $clientId");
$result = mysqli_query($conn, "SELECT * FROM tblclients WHERE id = '$clientId'");
$result = full_query("SELECT * FROM tblclients WHERE id = " . $_GET['id']);

// ✅ ALWAYS DO THIS
$result = Capsule::table('tblclients')
    ->where('id', '=', $clientId)
    ->first();
```

---

## 5. Module Development

### Module Naming Rules (All Types)

- Names must be **all lowercase**.
- Names must **start with a letter**.
- Only **letters, numbers, and underscores** are allowed.
- Name must be **unique** across the WHMCS installation.
- The directory name and main PHP filename must match exactly.

---

### 5.1 Addon Modules

**Path**: `/modules/addons/{modulename}/`

**Official Sample**: [github.com/WHMCS/sample-addon-module](https://github.com/WHMCS/sample-addon-module)

#### Required Functions

| Function                     | Purpose                                  |
|------------------------------|------------------------------------------|
| `{name}_config()`            | Module metadata + configuration fields   |
| `{name}_activate()`          | Runs on module activation (create tables)|
| `{name}_deactivate()`        | Runs on module deactivation (cleanup)    |
| `{name}_output($vars)`       | Admin area page output (echo, not return)|
| `{name}_clientarea($vars)`   | Client area output (return array)        |
| `{name}_upgrade($vars)`      | Handles version upgrades (schema changes)|

#### Config Function Template

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

use Illuminate\Database\Capsule\Manager as Capsule;
use WHMCS\Module\Addon\Setting;

function mymodule_config(): array
{
    return [
        'name'        => 'My Module',
        'description' => 'A brief description of what this module does.',
        'version'     => '1.0.0',
        'author'      => 'Your Name',
        'language'    => 'english',
        'fields'      => [
            'apiUrl' => [
                'FriendlyName' => 'API URL',
                'Type'         => 'text',
                'Size'         => '60',
                'Default'      => 'https://api.example.com',
                'Description'  => 'The base URL of the external API.',
                'Validate'     => 'url',
            ],
            'apiKey' => [
                'FriendlyName' => 'API Key',
                'Type'         => 'password',
                'Size'         => '40',
                'Description'  => 'Your API authentication key.',
                'Validate'     => 'required',
            ],
            'enableDebug' => [
                'FriendlyName' => 'Debug Mode',
                'Type'         => 'yesno',
                'Description'  => 'Enable verbose logging for troubleshooting.',
            ],
            'syncInterval' => [
                'FriendlyName' => 'Sync Interval',
                'Type'         => 'dropdown',
                'Options'      => '1 Hour,6 Hours,12 Hours,24 Hours',
                'Default'      => '6 Hours',
                'Description'  => 'How often to sync data.',
            ],
            'maxRetries' => [
                'FriendlyName' => 'Max API Retries',
                'Type'         => 'text',
                'Size'         => '5',
                'Default'      => '3',
                'Description'  => 'Maximum number of API retry attempts.',
                'Validate'     => 'numeric',
            ],
        ],
    ];
}
```

#### Activate / Deactivate Template

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

use Illuminate\Database\Capsule\Manager as Capsule;

function mymodule_activate(): array
{
    try {
        // Create main data table
        Capsule::schema()->create('mod_mymodule_data', function ($table) {
            /** @var \Illuminate\Database\Schema\Blueprint $table */
            $table->increments('id');
            $table->unsignedInteger('client_id');
            $table->string('key', 255);
            $table->text('value')->nullable();
            $table->timestamps();

            $table->index('client_id');
            $table->unique(['client_id', 'key']);
        });

        // Create settings table
        Capsule::schema()->create('mod_mymodule_settings', function ($table) {
            $table->increments('id');
            $table->string('setting_key', 100);
            $table->text('setting_value')->nullable();
            $table->timestamps();

            $table->unique('setting_key');
        });

        // Create audit log table
        Capsule::schema()->create('mod_mymodule_audit_log', function ($table) {
            $table->increments('id');
            $table->unsignedInteger('client_id');
            $table->string('action', 50);
            $table->text('details')->nullable();
            $table->string('ip_address', 45);
            $table->timestamps();

            $table->index('client_id');
            $table->index('action');
            $table->index('created_at');
        });

        return [
            'status'      => 'success',
            'description' => 'Module activated successfully. Configure settings above.',
        ];
    } catch (\Exception $e) {
        return [
            'status'      => 'error',
            'description' => 'Failed to create database table: ' . $e->getMessage(),
        ];
    }
}

function mymodule_deactivate(): array
{
    try {
        // Drop tables in reverse order (foreign key dependencies)
        Capsule::schema()->dropIfExists('mod_mymodule_audit_log');
        Capsule::schema()->dropIfExists('mod_mymodule_settings');
        Capsule::schema()->dropIfExists('mod_mymodule_data');

        return [
            'status'      => 'success',
            'description' => 'Module deactivated and data cleaned up.',
        ];
    } catch (\Exception $e) {
        return [
            'status'      => 'error',
            'description' => 'Failed to remove database table: ' . $e->getMessage(),
        ];
    }
}
```

#### Admin Output Function

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function mymodule_output($vars): void
{
    $moduleLink = $vars['modulelink'];
    $version    = $vars['version'];
    $apiUrl     = $vars['apiUrl'];
    $apiKey     = $vars['apiKey'];
    $LANG       = $vars['_lang'];

    $action = $_GET['action'] ?? 'index';

    // Validate action parameter
    $allowedActions = ['index', 'settings', 'logs', 'export'];
    if (!in_array($action, $allowedActions, true)) {
        $action = 'index';
    }

    switch ($action) {
        case 'settings':
            echo '<h2>' . htmlspecialchars($LANG['settings_title'] ?? 'Settings') . '</h2>';
            // Settings form logic
            break;

        case 'logs':
            echo '<h2>' . htmlspecialchars($LANG['logs_title'] ?? 'Activity Logs') . '</h2>';
            // Logs display logic
            break;

        case 'export':
            // Export functionality
            break;

        default:
            echo '<h2>' . htmlspecialchars($LANG['dashboard_title'] ?? 'Dashboard') . '</h2>';
            echo '<p>Module Version: ' . htmlspecialchars($version) . '</p>';
            echo '<a href="' . htmlspecialchars($moduleLink . '&action=settings') . '" '
               . 'class="btn btn-default">Settings</a>';
            break;
    }
}
```

#### Client Area Function

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

use Illuminate\Database\Capsule\Manager as Capsule;

function mymodule_clientarea($vars): array
{
    $moduleLink = $vars['modulelink'];
    $LANG       = $vars['_lang'];

    $action = $_GET['action'] ?? 'index';
    
    // Fetch currently logged-in client using modern WHMCS model classes
    $client = \WHMCS\User\Client::getLoggedInClient();
    $clientId = $client ? $client->id : 0;

    // Validate client session
    if (empty($clientId)) {
        return [
            'pagetitle'    => 'Login Required',
            'templatefile' => 'templates/client/error',
            'requirelogin' => true,
            'vars'         => [
                'errorMessage' => 'Please log in to access this page.',
            ],
        ];
    }

    switch ($action) {
        case 'settings':
            return [
                'pagetitle'    => $LANG['client_settings_title'] ?? 'Settings',
                'breadcrumb'   => [
                    'index.php?m=mymodule' => 'My Module',
                    'index.php?m=mymodule&action=settings' => 'Settings',
                ],
                'templatefile' => 'templates/client/settings',
                'requirelogin' => true,
                'forcessl'     => true,
                'vars'         => [
                    'moduleLink' => $moduleLink,
                    'settings'   => mymodule_getClientSettings($clientId),
                ],
            ];

        default:
            return [
                'pagetitle'    => $LANG['client_page_title'] ?? 'My Module',
                'breadcrumb'   => [
                    'index.php?m=mymodule' => 'My Module',
                ],
                'templatefile' => 'templates/client/dashboard',
                'requirelogin' => true,
                'forcessl'     => true,
                'vars'         => [
                    'moduleLink' => $moduleLink,
                    'data'       => Capsule::table('mod_mymodule_data')
                        ->where('client_id', '=', $clientId)
                        ->get()
                        ->toArray(),
                ],
            ];
    }
}

/**
 * Get client-specific settings (defined as a standard helper function in procedural scope)
 */
function mymodule_getClientSettings(int $clientId): array
{
    return Capsule::table('mod_mymodule_settings')
        ->where('client_id', '=', $clientId)
        ->pluck('setting_value', 'setting_key')
        ->toArray();
}
```

---

### 5.2 Provisioning (Server) Modules

**Path**: `/modules/servers/{modulename}/`

**Official Sample**: [github.com/WHMCS/sample-provisioning-module](https://github.com/WHMCS/sample-provisioning-module)

#### Required & Optional Functions

| Function                          | Required | Purpose                                  |
|-----------------------------------|----------|------------------------------------------|
| `{name}_MetaData()`              | Yes      | Module metadata and capabilities         |
| `{name}_ConfigOptions()`         | Yes      | Product configuration fields             |
| `{name}_CreateAccount($params)`  | Yes      | Provision a new service                  |
| `{name}_SuspendAccount($params)` | Yes      | Suspend an overdue service               |
| `{name}_UnsuspendAccount($params)`| Yes     | Unsuspend after payment                  |
| `{name}_TerminateAccount($params)`| Yes     | Terminate a service                      |
| `{name}_Renew($params)`          | Optional | Handle renewal payments                  |
| `{name}_ChangePassword($params)` | Optional | Client-initiated password change         |
| `{name}_ChangePackage($params)`  | Optional | Handle upgrades/downgrades               |
| `{name}_TestConnection($params)` | Optional | Test server connectivity from admin      |
| `{name}_ClientArea($params)`     | Optional | Client area output for the service       |
| `{name}_AdminLink($params)`      | Optional | Quick-link on server config page         |
| `{name}_LoginLink($params)`      | Optional | Login link on product management page    |
| `{name}_UsageUpdate($params)`    | Optional | Daily disk/bandwidth import              |
| `{name}_AdminCustomButtonArray()`| Optional | Custom admin action buttons              |
| `{name}_ClientAreaCustomButtonArray()`| Optional | Custom client action buttons         |
| `{name}_ClientAreaAllowedFunctions()`| Optional | Hidden callable client functions      |
| `{name}_AdminServicesTabFields($params)`| Optional | Extra fields on admin services tab  |
| `{name}_AdminServicesTabFieldsSave($params)`| Optional | Save extra admin fields          |

#### Provisioning Return Pattern with Retry Logic

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function myserver_CreateAccount(array $params): string
{
    $maxRetries = (int)($params['configoption5'] ?? 3);
    $retryDelay = 5; // seconds

    for ($attempt = 1; $attempt <= $maxRetries; $attempt++) {
        try {
            $apiUrl   = $params['serverhostname'];
            $apiUser  = $params['serverusername'];
            $apiPass  = $params['serverpassword'];
            $domain   = $params['domain'];
            $username = $params['username'];
            $password = $params['password'];
            $package  = $params['configoption1'];

            // Make API call to create account on remote server
            $response = myserver_apiCall($apiUrl, 'createAccount', [
                'domain'   => $domain,
                'username' => $username,
                'password' => $password,
                'package'  => $package,
            ]);

            // Log the module call for debugging
            logModuleCall(
                'myserver',
                'CreateAccount',
                ['domain' => $domain, 'username' => $username, 'package' => $package],
                $response,
                null,
                [$apiPass] // Scrub password from logs
            );

            if ($response['success']) {
                logActivity("MyServer: Account {$domain} provisioned successfully.");
                return 'success';
            }

            // If not last attempt, wait before retry
            if ($attempt < $maxRetries) {
                sleep($retryDelay);
                continue;
            }

            return 'Error: ' . ($response['message'] ?? 'Unknown error after ' . $maxRetries . ' attempts');

        } catch (\Exception $e) {
            // Scrub sensitive data from parameters before logging to the Module Log
            logModuleCall(
                'myserver',
                'CreateAccount',
                ['domain' => $domain, 'username' => $username, 'package' => $package],
                $e->getMessage()
            );
            
            if ($attempt < $maxRetries) {
                sleep($retryDelay);
                continue;
            }
            
            return 'Error: ' . $e->getMessage();
        }
    }

    return 'Error: Max retries exceeded';
}
```

#### Key `$params` Variables (Provisioning)

```
$params['serverhostname']   // Server hostname
$params['serverusername']   // Server username
$params['serverpassword']   // Server password (use decrypt() if needed)
$params['serveraccesshash'] // Server access hash
$params['serversecure']     // SSL/TLS enabled (true/false)
$params['serverport']       // Server port
$params['domain']           // Client's domain
$params['username']         // Service username
$params['password']         // Service password
$params['clientsdetails']   // Array of client information
$params['configoption1']    // First config option value
$params['configoption2']    // Second config option value
$params['customfields']     // Custom field values
$params['serviceid']        // Service ID in tblhosting
$params['pid']              // Product ID
$params['model']            // Service model object (WHMCS 8+)
```

---

### 5.3 Domain Registrar Modules

**Path**: `/modules/registrars/{modulename}/`

**Official Sample**: [github.com/WHMCS/sample-registrar-module](https://github.com/WHMCS/sample-registrar-module)

#### Required & Optional Functions

| Function                           | Required | Purpose                              |
|------------------------------------|----------|--------------------------------------|
| `{name}_getConfigArray()`          | Yes      | Configuration fields                 |
| `{name}_RegisterDomain($params)`  | Yes      | Register a new domain                |
| `{name}_TransferDomain($params)`  | Yes      | Initiate domain transfer             |
| `{name}_RenewDomain($params)`     | Yes      | Renew an existing domain             |
| `{name}_GetNameservers($params)`  | Yes      | Retrieve current nameservers         |
| `{name}_SaveNameservers($params)` | Yes      | Update nameservers                   |
| `{name}_GetContactDetails($params)`| Optional| Get WHOIS contact info              |
| `{name}_SaveContactDetails($params)`| Optional| Update WHOIS contact info          |
| `{name}_GetEPPCode($params)`      | Optional | Retrieve EPP/auth code              |
| `{name}_GetRegistrarLock($params)` | Optional | Check registrar lock status         |
| `{name}_SaveRegistrarLock($params)`| Optional | Toggle registrar lock               |
| `{name}_GetDNS($params)`          | Optional | Get DNS host records                 |
| `{name}_SaveDNS($params)`         | Optional | Update DNS host records              |
| `{name}_GetEmailForwarding($params)`| Optional | Get email forwarding rules         |
| `{name}_SaveEmailForwarding($params)`| Optional | Update email forwarding           |
| `{name}_IDProtectToggle($params)` | Optional | Toggle ID Protection                 |
| `{name}_RegisterNameserver($params)`| Optional | Create private nameserver          |
| `{name}_ModifyNameserver($params)` | Optional | Update private nameserver           |
| `{name}_DeleteNameserver($params)` | Optional | Remove private nameserver           |
| `{name}_Sync($params)`            | Optional | Domain sync (status + expiry)        |
| `{name}_TransferSync($params)`    | Optional | Transfer status sync                 |
| `{name}_CheckAvailability($params)`| Optional | Domain availability check           |
| `{name}_GetDomainSuggestions($params)`| Optional | Domain name suggestions          |
| `{name}_RequestDelete($params)`   | Optional | Delete/cancel domain                 |

#### Registrar Return Pattern

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function myregistrar_RegisterDomain(array $params): array
{
    $sld = $params['sld'];
    $tld = $params['tld'];
    $domain = $sld . '.' . $tld;
    $regPeriod = $params['regperiod'];

    // Nameservers
    $ns1 = $params['ns1'];
    $ns2 = $params['ns2'];
    $ns3 = $params['ns3'] ?? '';
    $ns4 = $params['ns4'] ?? '';
    $ns5 = $params['ns5'] ?? '';

    // Contact details
    $firstName = $params['firstname'];
    $lastName  = $params['lastname'];
    $email     = $params['email'];
    // ... additional contact fields

    try {
        $response = myregistrar_apiCall('registerDomain', [
            'domain'    => $domain,
            'period'    => $regPeriod,
            'ns'        => array_filter([$ns1, $ns2, $ns3, $ns4, $ns5]),
            'contacts'  => [
                'firstName' => $firstName,
                'lastName'  => $lastName,
                'email'     => $email,
            ],
        ]);

        logModuleCall('myregistrar', 'RegisterDomain', [
            'domain' => $domain, 'period' => $regPeriod,
        ], $response);

        if ($response['success']) {
            logActivity("MyRegistrar: Domain {$domain} registered successfully.");
            return ['success' => true];
        }

        return ['error' => $response['message'] ?? 'Registration failed'];
    } catch (\Exception $e) {
        logModuleCall('myregistrar', 'RegisterDomain', $params, $e->getMessage());
        return ['error' => $e->getMessage()];
    }
}
```

#### Domain Sync Function

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function myregistrar_Sync(array $params): array
{
    $domain = $params['sld'] . '.' . $params['tld'];

    try {
        $response = myregistrar_apiCall('getDomainInfo', ['domain' => $domain]);

        logModuleCall('myregistrar', 'Sync', ['domain' => $domain], $response);

        // Validate response structure
        if (!isset($response['status']) || !isset($response['expiryDate'])) {
            return ['error' => 'Invalid response from registrar API'];
        }

        return [
            'active'          => ($response['status'] === 'active'),
            'cancelled'       => ($response['status'] === 'cancelled'),
            'transferredAway' => ($response['status'] === 'transferred'),
            'expirydate'      => $response['expiryDate'], // Format: YYYY-MM-DD
        ];
    } catch (\Exception $e) {
        logModuleCall('myregistrar', 'Sync', $params, $e->getMessage());
        return ['error' => $e->getMessage()];
    }
}
```

---

### 5.4 Payment Gateway Modules

**Path**: `/modules/gateways/{modulename}.php`

**Callback Path**: `/modules/gateways/callback/{modulename}.php`

**Official Samples**:

- Third-Party: [github.com/WHMCS/sample-gateway-module](https://github.com/WHMCS/sample-gateway-module)
- Merchant: [github.com/WHMCS/sample-merchant-gateway](https://github.com/WHMCS/sample-merchant-gateway)

#### Gateway Types

| Type              | User Experience                              | Key Function     |
|-------------------|----------------------------------------------|------------------|
| **Third-Party**   | Customer leaves site to pay, returns after   | `_link()`        |
| **Merchant**      | Card details entered in WHMCS, processed BG  | `_capture()`     |
| **Tokenised**     | Card details not stored locally, token used  | `_storeremote()` |

#### Third-Party Gateway — Link Function

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function mygateway_link(array $params): string
{
    // Gateway configuration
    $merchantId = $params['merchantId'];
    $secretKey  = $params['secretKey'];

    // Invoice details
    $invoiceId   = $params['invoiceid'];
    $amount      = $params['amount'];
    $currency    = $params['currency'];
    $description = $params['description'];

    // Client details
    $firstName = $params['clientdetails']['firstname'];
    $lastName  = $params['clientdetails']['lastname'];
    $email     = $params['clientdetails']['email'];

    // System URLs
    $systemUrl  = $params['systemurl'];
    $returnUrl  = $params['returnurl'];
    $callbackUrl = $systemUrl . 'modules/gateways/callback/mygateway.php';

    // Generate HMAC signature for security
    $signatureData = $invoiceId . $amount . $currency;
    $signature = hash_hmac('sha256', $signatureData, $secretKey);

    // Build the payment form
    $htmlOutput  = '<form method="POST" action="https://pay.example.com/checkout">';
    $htmlOutput .= '<input type="hidden" name="merchant_id" value="' . htmlspecialchars($merchantId) . '">';
    $htmlOutput .= '<input type="hidden" name="amount" value="' . htmlspecialchars($amount) . '">';
    $htmlOutput .= '<input type="hidden" name="currency" value="' . htmlspecialchars($currency) . '">';
    $htmlOutput .= '<input type="hidden" name="invoice_id" value="' . htmlspecialchars($invoiceId) . '">';
    $htmlOutput .= '<input type="hidden" name="return_url" value="' . htmlspecialchars($returnUrl) . '">';
    $htmlOutput .= '<input type="hidden" name="callback_url" value="' . htmlspecialchars($callbackUrl) . '">';
    $htmlOutput .= '<input type="hidden" name="email" value="' . htmlspecialchars($email) . '">';
    $htmlOutput .= '<input type="hidden" name="signature" value="' . htmlspecialchars($signature) . '">';
    $htmlOutput .= '<input type="submit" class="btn btn-primary" value="Pay Now">';
    $htmlOutput .= '</form>';

    return $htmlOutput;
}
```

#### Merchant Gateway — Capture Function

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function mygateway_capture(array $params): array
{
    $merchantId = $params['merchantId'];
    $secretKey  = $params['secretKey'];
    $invoiceId  = $params['invoiceid'];
    $amount     = $params['amount'];
    $currency   = $params['currency'];
    $cardNumber = $params['cardnum'];
    $cardExpiry = $params['cardexp'];
    $cardCvv    = $params['cccvv'];

    try {
        $response = mygateway_apiCall('charge', [
            'merchant_id' => $merchantId,
            'amount'      => $amount,
            'currency'    => $currency,
            'card'        => $cardNumber,
            'expiry'      => $cardExpiry,
            'cvv'         => $cardCvv,
            'reference'   => $invoiceId,
        ]);

        logModuleCall('mygateway', 'capture', [
            'amount' => $amount, 'invoice' => $invoiceId,
        ], $response, null, [$cardNumber, $cardCvv, $secretKey]);

        if ($response['status'] === 'approved') {
            logActivity("MyGateway: Payment approved for invoice #{$invoiceId}");
            return [
                'status'  => 'success',
                'transid' => $response['transaction_id'],
                'rawdata' => $response,
            ];
        }

        logActivity("MyGateway: Payment declined for invoice #{$invoiceId}");
        return [
            'status'  => 'declined',
            'rawdata' => $response,
        ];
    } catch (\Exception $e) {
        logModuleCall('mygateway', 'capture', $params, $e->getMessage());
        return [
            'status'  => 'error',
            'rawdata' => $e->getMessage(),
        ];
    }
}
```

#### Callback File Template with Signature Verification

```php
<?php

declare(strict_types=1);

// /modules/gateways/callback/mygateway.php

require_once __DIR__ . '/../../../init.php';
require_once __DIR__ . '/../../../includes/gatewayfunctions.php';
require_once __DIR__ . '/../../../includes/invoicefunctions.php';

$gatewayModuleName = basename(__FILE__, '.php');
$gatewayParams = getGatewayVariables($gatewayModuleName);

if (!$gatewayParams['type']) {
    die("Module not activated");
}

// Read and validate the callback data
$transactionId   = $_POST['transaction_id'] ?? '';
$invoiceId       = $_POST['invoice_id'] ?? '';
$transactionAmount = $_POST['amount'] ?? '';
$paymentStatus   = $_POST['status'] ?? '';
$signature       = $_POST['signature'] ?? '';

// Validate required fields
if (empty($transactionId) || empty($invoiceId) || empty($transactionAmount)) {
    logTransaction($gatewayModuleName, $_POST, 'Missing required fields');
    die("Missing required fields");
}

// Verify the callback signature
$expectedSignature = hash_hmac(
    'sha256',
    $transactionId . $invoiceId . $transactionAmount,
    $gatewayParams['secretKey']
);

if (!hash_equals($expectedSignature, $signature)) {
    logTransaction($gatewayModuleName, $_POST, 'Invalid Signature');
    die("Invalid signature");
}

// Validate invoice ID
$invoiceId = checkCbInvoiceID($invoiceId, $gatewayModuleName);

// Check for duplicate transaction
checkCbTransID($transactionId);

if ($paymentStatus === 'completed') {
    // Add payment to the invoice
    addInvoicePayment(
        $invoiceId,
        $transactionId,
        $transactionAmount,
        0, // Payment fee
        $gatewayModuleName
    );

    logTransaction($gatewayModuleName, $_POST, 'Successful');
} else {
    logTransaction($gatewayModuleName, $_POST, 'Payment Failed: ' . $paymentStatus);
}
```

---

## 6. Action Hooks

### Hook File Locations

| Location                          | Scope                          |
|-----------------------------------|--------------------------------|
| `/includes/hooks/*.php`           | Global hooks (always active)   |
| `/modules/addons/{name}/hooks.php`| Module hooks (when activated)  |
| `/modules/servers/{name}/hooks.php`| Server module hooks           |

> **Important**: If you add `hooks.php` to a module *after* it has been
> activated, you must deactivate and reactivate the module (or re-save
> module settings) for WHMCS to recognise the new hooks file.

### Hook Syntax

```php
<?php

// Using a closure (anonymous function)
add_hook('ClientAdd', 1, function (array $vars) {
    $clientId  = $vars['userid'];
    $firstName = $vars['firstname'];
    $lastName  = $vars['lastname'];
    $email     = $vars['email'];

    logActivity("New client registered: {$firstName} {$lastName} (#{$clientId})");
});

// Using a named function (recommended for complex hooks)
add_hook('InvoicePaid', 1, 'mymodule_hookInvoicePaid');

function mymodule_hookInvoicePaid(array $vars): void
{
    $invoiceId = $vars['invoiceid'];

    // Fetch invoice details via Internal API
    $invoice = localAPI('GetInvoice', ['invoiceid' => $invoiceId]);

    if ($invoice['result'] === 'success') {
        logActivity("Invoice #{$invoiceId} paid. Total: {$invoice['total']}");
    }
}
```

### Priority System

- Priority is an integer. Lower numbers execute first.
- Use `1` for most hooks (default/highest priority).
- Use `10+` for hooks that should run after others.

### Most-Used Hook Points (Quick Reference)

#### Client Lifecycle

| Hook                     | Trigger                             |
|--------------------------|-------------------------------------|
| `ClientAdd`              | A new client account is created     |
| `ClientEdit`             | Client profile is updated           |
| `ClientClose`            | Client account is closed            |
| `ClientDelete`           | Client account is deleted           |
| `ClientChangePassword`   | Client changes their password       |

#### Billing & Invoices

| Hook                     | Trigger                             |
|--------------------------|-------------------------------------|
| `InvoiceCreated`         | A new invoice is created            |
| `InvoicePaid`            | An invoice is marked as paid        |
| `InvoiceRefunded`        | An invoice is refunded              |
| `InvoiceCancelled`       | An invoice is cancelled             |
| `AddInvoicePayment`      | A payment is applied to an invoice  |

#### Module Events

| Hook                     | Trigger                             |
|--------------------------|-------------------------------------|
| `AfterModuleCreate`      | After a service is provisioned      |
| `AfterModuleSuspend`     | After a service is suspended        |
| `AfterModuleUnsuspend`   | After a service is unsuspended      |
| `AfterModuleTerminate`   | After a service is terminated       |
| `PreModuleCreate`        | Before provisioning (can abort)     |

#### Support Tickets

| Hook                     | Trigger                             |
|--------------------------|-------------------------------------|
| `TicketOpen`             | A new ticket is submitted           |
| `TicketAdminReply`       | An admin replies to a ticket        |
| `TicketUserReply`        | A client replies to a ticket        |
| `TicketClose`            | A ticket is closed                  |
| `TicketStatusChange`     | Ticket status changes               |

#### Domain Events

| Hook                     | Trigger                             |
|--------------------------|-------------------------------------|
| `AfterRegistrarRegistration` | Domain registration completes   |
| `AfterRegistrarTransfer` | Domain transfer completes           |
| `AfterRegistrarRenewal`  | Domain renewal completes            |

> **Full Hook Index**: [developers.whmcs.com/hooks/hook-index/](https://developers.whmcs.com/hooks/hook-index/)
> **Hook Reference Manual**: [developers.whmcs.com/hooks-reference/](https://developers.whmcs.com/hooks-reference/)

---

## 7. API Integration

### Internal API (Local API)

Use `localAPI()` when making API calls from **within** the WHMCS runtime
(modules, hooks, or custom pages). No authentication required — the call
runs with admin-level privileges.

```php
<?php

// Example: Get client details
$results = localAPI('GetClientsDetails', [
    'clientid' => $clientId,
    'stats'    => true,
]);

if ($results['result'] === 'success') {
    $clientName = $results['fullname'] ?? 'Unknown';
    $email      = $results['email'] ?? '';
    $status     = $results['status'] ?? '';
}

// Example: Add a credit
localAPI('AddCredit', [
    'clientid' => $clientId,
    'amount'   => 10.00,
    'description' => 'Welcome credit applied by module',
]);

// Example: Open a ticket
localAPI('OpenTicket', [
    'clientid'   => $clientId,
    'deptid'     => 1,
    'subject'    => 'Account Provisioned',
    'message'    => 'Your account has been provisioned successfully.',
    'priority'   => 'Medium',
]);

// Example: Send an email
localAPI('SendEmail', [
    'messagename' => 'Service Welcome Email',
    'id'          => $serviceId,
]);
```

> **Important**: `localAPI()` does **not** require `init.php` when called
> from within hooks or modules (it's already loaded). Only include
> `init.php` when calling from standalone scripts.

### External API (Remote Calls)

Use `WHMCS\Module\Guzzle` or `GuzzleHttp\Client` (bundled with WHMCS) for
external HTTP requests.

```php
<?php

use GuzzleHttp\Client;
use GuzzleHttp\Exception\RequestException;
use GuzzleHttp\Exception\ConnectException;
use GuzzleHttp\Exception\TimeoutException;

function mymodule_apiCall(
    string $endpoint,
    array $data,
    int $timeout = 30,
    int $retries = 3
): array {
    $client = new Client([
        'base_uri' => 'https://api.example.com/v1/',
        'timeout'  => $timeout,
        'headers'  => [
            'Authorization' => 'Bearer ' . $apiKey,
            'Content-Type'  => 'application/json',
            'Accept'        => 'application/json',
        ],
    ]);

    $lastException = null;

    for ($attempt = 1; $attempt <= $retries; $attempt++) {
        try {
            $response = $client->post($endpoint, [
                'json' => $data,
            ]);

            $result = json_decode($response->getBody()->getContents(), true);

            logModuleCall('mymodule', $endpoint, $data, $result);

            return $result;
        } catch (ConnectException $e) {
            $lastException = $e;
            logModuleCall('mymodule', $endpoint . " (attempt {$attempt})", $data, $e->getMessage());
            
            if ($attempt < $retries) {
                sleep(2 * $attempt); // Exponential backoff
                continue;
            }
        } catch (TimeoutException $e) {
            $lastException = $e;
            logModuleCall('mymodule', $endpoint . " (attempt {$attempt})", $data, $e->getMessage());
            
            if ($attempt < $retries) {
                sleep(2 * $attempt);
                continue;
            }
        } catch (RequestException $e) {
            $errorResponse = $e->hasResponse()
                ? $e->getResponse()->getBody()->getContents()
                : $e->getMessage();

            logModuleCall('mymodule', $endpoint, $data, $errorResponse);

            throw new \RuntimeException('API call failed: ' . $errorResponse);
        }
    }

    throw new \RuntimeException(
        'API call failed after ' . $retries . ' attempts: ' . $lastException->getMessage()
    );
}
```

> **API Index**: [developers.whmcs.com/api/api-index/](https://developers.whmcs.com/api/api-index/)
> **API Reference**: [developers.whmcs.com/api-reference/](https://developers.whmcs.com/api-reference/)

---

## 8. Templating & UI

### Smarty Template Rules

- All templates use `.tpl` extension and reside within the module's
  `templates/` directory.
- **Never** use the deprecated `{php}` tag — it is removed in Smarty v4.
- Use `{$variable}` for output and `{if}`, `{foreach}` for logic.
- Escape output with `{$variable|escape:'html'}` for user-generated content.

### Admin Area UI — Native CSS Classes

Use WHMCS built-in Bootstrap and admin CSS classes for a native look:

```html
{* templates/admin/dashboard.tpl *}

<div class="tab-content">

    {* Stats Cards *}
    <div class="row">
        <div class="col-sm-4">
            <div class="panel panel-default">
                <div class="panel-heading">
                    <h3 class="panel-title">Total Clients</h3>
                </div>
                <div class="panel-body text-center">
                    <h1>{$totalClients}</h1>
                </div>
            </div>
        </div>
    </div>

    {* Data Table *}
    <table id="myModuleTable" class="datatable" width="100%">
        <thead>
            <tr>
                <th>ID</th>
                <th>Client</th>
                <th>Status</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            {foreach from=$items item=row}
            <tr>
                <td>{$row.id}</td>
                <td>{$row.client_name|escape:'html'}</td>
                <td>
                    <span class="label label-{if $row.status == 'Active'}success{else}danger{/if}">
                        {$row.status}
                    </span>
                </td>
                <td>
                    <a href="{$moduleLink}&action=view&id={$row.id}" class="btn btn-sm btn-default">
                        <i class="fas fa-eye"></i> View
                    </a>
                </td>
            </tr>
            {foreachelse}
            <tr>
                <td colspan="4" class="text-center">No records found.</td>
            </tr>
            {/foreach}
        </tbody>
    </table>

</div>
```

### Client Area UI

```html
{* templates/client/dashboard.tpl *}

<div class="row">
    <div class="col-md-12">
        <h3>{$LANG.module_dashboard_title}</h3>

        {if $data|count > 0}
        <div class="table-responsive">
            <table class="table table-striped table-hover">
                <thead>
                    <tr>
                        <th>{$LANG.column_id}</th>
                        <th>{$LANG.column_name}</th>
                        <th>{$LANG.column_status}</th>
                    </tr>
                </thead>
                <tbody>
                    {foreach from=$data item=item}
                    <tr>
                        <td>{$item.id}</td>
                        <td>{$item.key|escape:'html'}</td>
                        <td>{$item.value|escape:'html'}</td>
                    </tr>
                    {/foreach}
                </tbody>
            </table>
        </div>
        {else}
        <div class="alert alert-info">
            {$LANG.no_records_found}
        </div>
        {/if}
    </div>
</div>
```

---

## 9. Security Hardening & Best Practices

### Security Checklist

```
☑  WHMCS Access Guard     defined("WHMCS") or die("Access Denied");
☑  Parameter Binding      Use Capsule for all DB queries (auto-binding)
☑  Input Validation       Validate/sanitise all $_POST / $_GET input
☑  CSRF Protection        check_token in POST requests (admin area)
☑  Credential Security    Use encrypt()/decrypt() for stored secrets
☑  API Key Scrubbing      Pass secrets to $replaceVars in logModuleCall()
☑  Output Escaping        Use htmlspecialchars() or Smarty |escape
☑  Error Masking          Log detailed errors; show generic messages to users
☑  File Permissions       644 for files, 755 for directories
☑  No Direct DB Access    Never use mysql_*/mysqli_* — always Capsule
☑  HTTPS Enforcement      Use 'forcessl' => true in client area output
☑  Rate Limiting          Implement API call rate limits where applicable
☑  Signature Validation   Verify HMAC signatures in gateway callbacks
☑  SQL Injection Prevention   Always use parameterized queries
☑  XSS Prevention         Escape all output, validate all input
☑  Content Security Policy    Set CSP headers for admin pages
☑  Session Security       Regenerate session IDs after login
☑  File Upload Validation Validate file types and sizes
☑  Directory Traversal Prevention   Validate file paths
☑  API Rate Limiting      Implement exponential backoff
```

### Input Validation Patterns

```php
<?php

// Validate email
function validateEmail(string $email): bool {
    return filter_var($email, FILTER_VALIDATE_EMAIL) !== false;
}

// Validate URL
function validateUrl(string $url): bool {
    return filter_var($url, FILTER_VALIDATE_URL) !== false;
}

// Validate integer
function validateInteger($value, int $min = PHP_INT_MIN, int $max = PHP_INT_MAX): bool {
    $value = filter_var($value, FILTER_VALIDATE_INT, [
        'options' => ['min_range' => $min, 'max_range' => $max]
    ]);
    return $value !== false;
}

// Sanitize string for database
function sanitizeString(string $input): string {
    return htmlspecialchars(trim($input), ENT_QUOTES, 'UTF-8');
}

// Validate and sanitize array input
function validateArrayInput(array $input, array $rules): array {
    $validated = [];
    
    foreach ($rules as $field => $rule) {
        if (!isset($input[$field])) {
            if ($rule['required'] ?? false) {
                throw new \InvalidArgumentException("Field {$field} is required");
            }
            continue;
        }
        
        $value = $input[$field];
        
        // Apply validation
        if (isset($rule['type'])) {
            switch ($rule['type']) {
                case 'email':
                    if (!filter_var($value, FILTER_VALIDATE_EMAIL)) {
                        throw new \InvalidArgumentException("Invalid email for {$field}");
                    }
                    break;
                case 'url':
                    if (!filter_var($value, FILTER_VALIDATE_URL)) {
                        throw new \InvalidArgumentException("Invalid URL for {$field}");
                    }
                    break;
                case 'int':
                    if (!filter_var($value, FILTER_VALIDATE_INT)) {
                        throw new \InvalidArgumentException("Invalid integer for {$field}");
                    }
                    break;
            }
        }
        
        // Apply sanitization
        $validated[$field] = htmlspecialchars(trim($value), ENT_QUOTES, 'UTF-8');
    }
    
    return $validated;
}
```

### HMAC Signature Verification

```php
<?php

// Generate signature
function generateSignature(array $data, string $secret): string {
    $payload = json_encode($data, JSON_UNESCAPED_SLASHES | JSON_UNESCAPED_UNICODE);
    return hash_hmac('sha256', $payload, $secret);
}

// Verify signature
function verifySignature(array $data, string $signature, string $secret): bool {
    $expected = generateSignature($data, $secret);
    return hash_equals($expected, $signature);
}
```

### Rate Limiting Implementation

```php
<?php

use Illuminate\Database\Capsule\Manager as Capsule;

class RateLimiter {
    private string $key;
    private int $maxAttempts;
    private int $decayMinutes;

    public function __construct(string $key, int $maxAttempts = 60, int $decayMinutes = 1) {
        $this->key = $key;
        $this->maxAttempts = $maxAttempts;
        $this->decayMinutes = $decayMinutes;
    }

    public function attempt(): bool {
        $attempts = $this->getAttempts();
        
        if ($attempts >= $this->maxAttempts) {
            return false;
        }

        $this->increment();
        return true;
    }

    private function getAttempts(): int {
        $record = Capsule::table('rate_limits')
            ->where('key', $this->key)
            ->first();

        if (!$record || strtotime($record->decay_at) < time()) {
            $this->reset();
            return 0;
        }

        return $record->attempts;
    }

    private function increment(): void {
        Capsule::table('rate_limits')->updateOrInsert(
            ['key' => $this->key],
            [
                'attempts' => Capsule::raw('attempts + 1'),
                'decay_at' => date('Y-m-d H:i:s', strtotime("+{$this->decayMinutes} minutes")),
            ]
        );
    }

    private function reset(): void {
        Capsule::table('rate_limits')->where('key', $this->key)->delete();
    }
}

// Usage
$limiter = new RateLimiter('api:' . $clientId, 100, 60);
if (!$limiter->attempt()) {
    http_response_code(429);
    die('Too many requests');
}
```

---

## 10. Testing Strategy

### Testing Framework Setup

```bash
# Install PHPUnit
composer require --dev phpunit/phpunit:^10.0

# Install Mockery for mocking
composer require --dev mockery/mockery:^1.6

# Install Faker for test data
composer require --dev fakerphp/faker

# Create tests/bootstrap.php to mock WHMCS global functions
mkdir -p tests
cat > tests/bootstrap.php << 'EOF'
<?php
// Mock WHMCS Environment for PHPUnit
if (!defined('WHMCS')) {
    define('WHMCS', true);
}

// Stub WHMCS global utility functions to prevent undefined function errors
if (!function_exists('logActivity')) {
    function logActivity(string $description, int $clientId = 0): void {}
}

if (!function_exists('logModuleCall')) {
    function logModuleCall(string $module, string $action, mixed $request, mixed $response, mixed $processed = '', array $replaceVars = []): void {}
}

if (!function_exists('encrypt')) {
    function encrypt(string $string): string { return base64_encode($string); }
}

if (!function_exists('decrypt')) {
    function decrypt(string $string): string { return base64_decode($string); }
}

if (!function_exists('localAPI')) {
    function localAPI(string $action, array $params, string $adminUser = ''): array { return ['result' => 'success']; }
}

require_once __DIR__ . '/../vendor/autoload.php';
EOF

# Create phpunit.xml
cat > phpunit.xml << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="tests/bootstrap.php"
         colors="true"
         verbose="true">
    <testsuites>
        <testsuite name="WHMCS Module Tests">
            <directory>tests/Unit</directory>
            <directory>tests/Integration</directory>
        </testsuite>
    </testsuites>
    <coverage>
        <include>
            <directory suffix=".php">src/</directory>
        </include>
    </coverage>
</phpunit>
EOF
```

### Unit Test Example

```php
<?php

declare(strict_types=1);

namespace Tests\Unit;

use PHPUnit\Framework\TestCase;
use Mockery;
use Illuminate\Database\Capsule\Manager as Capsule;

class MyModuleTest extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();
        
        // Initialize Capsule for testing
        $capsule = new Capsule;
        $capsule->addConnection([
            'driver'    => 'sqlite',
            'database'  => ':memory:',
        ]);
        $capsule->setAsGlobal();
        $capsule->bootEloquent();
    }

    protected function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }

    public function testValidateEmail(): void
    {
        $this->assertTrue(validateEmail('test@example.com'));
        $this->assertFalse(validateEmail('invalid-email'));
        $this->assertFalse(validateEmail(''));
    }

    public function testValidateUrl(): void
    {
        $this->assertTrue(validateUrl('https://example.com'));
        $this->assertTrue(validateUrl('http://example.com/path'));
        $this->assertFalse(validateUrl('not-a-url'));
    }

    public function testSanitizeString(): void
    {
        $this->assertEquals('test', sanitizeString('test'));
        $this->assertEquals('&amp;', sanitizeString('&'));
        $this->assertEquals('&lt;script&gt;', sanitizeString('<script>'));
    }

    public function testGenerateSignature(): void
    {
        $data = ['invoice_id' => 123, 'amount' => 100];
        $secret = 'test-secret';
        
        $signature = generateSignature($data, $secret);
        
        $this->assertIsString($signature);
        $this->assertEquals(64, strlen($signature)); // SHA-256 hex length
    }

    public function testVerifySignature(): void
    {
        $data = ['invoice_id' => 123, 'amount' => 100];
        $secret = 'test-secret';
        
        $signature = generateSignature($data, $secret);
        
        $this->assertTrue(verifySignature($data, $signature, $secret));
        $this->assertFalse(verifySignature($data, 'invalid', $secret));
    }
}
```

### Integration Test Example

```php
<?php

declare(strict_types=1);

namespace Tests\Integration;

use PHPUnit\Framework\TestCase;
use Illuminate\Database\Capsule\Manager as Capsule;

class DatabaseTest extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();
        
        $capsule = new Capsule;
        $capsule->addConnection([
            'driver'    => 'sqlite',
            'database'  => ':memory:',
        ]);
        $capsule->setAsGlobal();
        $capsule->bootEloquent();
        
        // Create test tables
        Capsule::schema()->create('mod_test_data', function ($table) {
            $table->increments('id');
            $table->unsignedInteger('client_id');
            $table->string('key', 255);
            $table->text('value')->nullable();
            $table->timestamps();
        });
    }

    public function testInsertAndRetrieve(): void
    {
        // Insert data
        $id = Capsule::table('mod_test_data')->insertGetId([
            'client_id' => 1,
            'key' => 'test_key',
            'value' => 'test_value',
            'created_at' => date('Y-m-d H:i:s'),
        ]);
        
        // Retrieve data
        $record = Capsule::table('mod_test_data')->where('id', $id)->first();
        
        $this->assertNotNull($record);
        $this->assertEquals(1, $record->client_id);
        $this->assertEquals('test_key', $record->key);
        $this->assertEquals('test_value', $record->value);
    }

    public function testUpdateRecord(): void
    {
        // Insert
        $id = Capsule::table('mod_test_data')->insertGetId([
            'client_id' => 1,
            'key' => 'test_key',
            'value' => 'original',
            'created_at' => date('Y-m-d H:i:s'),
        ]);
        
        // Update
        Capsule::table('mod_test_data')
            ->where('id', $id)
            ->update(['value' => 'updated']);
        
        // Verify
        $record = Capsule::table('mod_test_data')->where('id', $id)->first();
        $this->assertEquals('updated', $record->value);
    }

    public function testDeleteRecord(): void
    {
        // Insert
        $id = Capsule::table('mod_test_data')->insertGetId([
            'client_id' => 1,
            'key' => 'test_key',
            'value' => 'to_delete',
            'created_at' => date('Y-m-d H:i:s'),
        ]);
        
        // Delete
        Capsule::table('mod_test_data')->where('id', $id)->delete();
        
        // Verify
        $record = Capsule::table('mod_test_data')->where('id', $id)->first();
        $this->assertNull($record);
    }
}
```

### Running Tests

```bash
# Run all tests
vendor/bin/phpunit

# Run with coverage
vendor/bin/phpunit --coverage-html coverage/

# Run specific test file
vendor/bin/phpunit tests/Unit/MyModuleTest.php

# Run specific test method
vendor/bin/phpunit --filter testValidateEmail
```

---

## 11. Performance Optimization

### Database Query Optimization

```php
<?php

// ❌ BAD — Select all columns
$clients = Capsule::table('tblclients')->get();

// ✅ GOOD — Select only needed columns
$clients = Capsule::table('tblclients')
    ->select('id', 'firstname', 'lastname', 'email')
    ->get();

// ❌ BAD — N+1 query problem
$services = Capsule::table('tblhosting')->get();
foreach ($services as $service) {
    $client = Capsule::table('tblclients')
        ->where('id', $service->userid)
        ->first(); // Additional query for each service!
}

// ✅ GOOD — Use joins
$services = Capsule::table('tblhosting')
    ->join('tblclients', 'tblhosting.userid', '=', 'tblclients.id')
    ->select('tblhosting.*', 'tblclients.firstname', 'tblclients.lastname')
    ->get();

// ✅ GOOD — Use eager loading with chunking
Capsule::table('tblhosting')
    ->chunk(100, function ($services) {
        foreach ($services as $service) {
            // Process in chunks of 100
        }
    });

// ✅ GOOD — Use caching for expensive queries (retrieving the PSR-16 cache instance from the DIC container)
function getActiveClients(): array {
    $cacheKey = 'active_clients_' . date('Y-m-d');
    $cache = \DI::make('cache');
    
    if ($cache->has($cacheKey)) {
        return $cache->get($cacheKey);
    }
    
    $clients = Capsule::table('tblclients')
        ->where('status', '=', 'Active')
        ->get()
        ->toArray();
        
    $cache->set($cacheKey, $clients, 3600);
    return $clients;
}
```

### Indexing Strategy

```php
<?php

// Add indexes for frequently queried columns
Capsule::schema()->table('tblhosting', function ($table) {
    $table->index('userid');      // Foreign key
    $table->index('status');      // Filter column
    $table->index('packageid');   // Join column
    $table->index(['status', 'userid']); // Composite index
});

// Add indexes for custom tables
Capsule::schema()->table('mod_mymodule_data', function ($table) {
    $table->index('client_id');
    $table->index('key');
    $table->index('created_at');
    $table->index(['client_id', 'key']); // Composite unique
});
```

### Caching Implementation

```php
<?php

class ModuleCache {
    private string $prefix;
    private int $defaultTTL;
    private $cache;

    public function __construct(string $prefix = 'mymodule', int $defaultTTL = 3600) {
        $this->prefix = $prefix;
        $this->defaultTTL = $defaultTTL;
        // Retrieve WHMCS internal PSR-16 cache implementation from DIC
        $this->cache = \DI::make('cache');
    }

    public function get(string $key, callable $callback, int $ttl = null): mixed {
        $cacheKey = $this->prefix . ':' . $key;
        $ttl = $ttl ?? $this->defaultTTL;

        if ($this->cache->has($cacheKey)) {
            return $this->cache->get($cacheKey);
        }

        $value = $callback();
        $this->cache->set($cacheKey, $value, $ttl);
        return $value;
    }

    public function put(string $key, mixed $value, int $ttl = null): bool {
        $cacheKey = $this->prefix . ':' . $key;
        $ttl = $ttl ?? $this->defaultTTL;

        return $this->cache->set($cacheKey, $value, $ttl);
    }

    public function forget(string $key): bool {
        $cacheKey = $this->prefix . ':' . $key;
        return $this->cache->delete($cacheKey);
    }

    public function flush(): bool {
        return $this->cache->clear();
    }
}

// Usage
$cache = new ModuleCache('mymodule', 3600);

// Cache for 1 hour
$clientData = $cache->get('client_' . $clientId, function () use ($clientId) {
    return Capsule::table('tblclients')
        ->where('id', $clientId)
        ->first();
}, 3600);
```

### Connection Pooling (WHMCS 8.11+)

```php
<?php

// WHMCS 8.11+ supports connection pooling
// Configure in configuration.php
$config['db_connection_pooling'] = true;
$config['db_pool_size'] = 10;
$config['db_pool_timeout'] = 30;
```

---

## 12. Error Handling & Logging

### logModuleCall() — Module Debug Log

Records API interactions viewable in Configuration → System Logs → Module Log.

```php
<?php

logModuleCall(
    'mymodule',           // Module name
    'CreateAccount',      // Action being performed
    $requestData,         // Input parameters (string or array)
    $rawResponse,         // Raw API response
    $processedResponse,   // Processed/decoded response (optional)
    [                     // Sensitive strings to scrub from logs
        $apiPassword,
        $apiKey,
        $cardNumber,
    ]
);
```

### logActivity() — System Activity Log

Records an entry in the WHMCS System Activity Log (visible to admins).

```php
<?php

logActivity("MyModule: Client #{$clientId} provisioned on server {$serverName}");
logActivity("MyModule Error: Failed to connect to API - " . $e->getMessage());
```

### Custom Exception Classes

```php
<?php

declare(strict_types=1);

namespace MyModule\Exceptions;

class ModuleException extends \RuntimeException {
    protected int $moduleId;
    protected array $context;

    public function __construct(
        string $message,
        int $moduleId = 0,
        array $context = [],
        int $code = 0,
        ?\Throwable $previous = null
    ) {
        parent::__construct($message, $code, $previous);
        $this->moduleId = $moduleId;
        $this->context = $context;
    }

    public function getModuleId(): int {
        return $this->moduleId;
    }

    public function getContext(): array {
        return $this->context;
    }
}

class ApiException extends ModuleException {}
class ValidationException extends ModuleException {}
class DatabaseException extends ModuleException {}
```

### Module Function Error Returns

```php
<?php

// Provisioning modules: return a string
// 'success' on success, error message string on failure
return 'success';
return 'Error: Could not connect to server';

// Registrar modules: return an array
return ['success' => true];
return ['error' => 'Domain not found at registrar'];

// Addon activate/deactivate: return status array
return ['status' => 'success', 'description' => 'Module activated.'];
return ['status' => 'error', 'description' => 'Table creation failed.'];

// Gateway capture: return status array
return ['status' => 'success', 'transid' => $txnId, 'rawdata' => $response];
return ['status' => 'declined', 'rawdata' => $response];
return ['status' => 'error', 'rawdata' => $errorMessage];
```

---

## 13. Module Upgrade Pattern

Use the `_upgrade` function to manage database schema changes between
versions. WHMCS calls this function automatically when the version in
`_config()` is higher than the previously-stored version.

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

use Illuminate\Database\Capsule\Manager as Capsule;

function mymodule_upgrade(array $vars): void
{
    $currentVersion = $vars['version'];

    // Version 1.1.0: Add status column
    if (version_compare($currentVersion, '1.1.0', '<')) {
        try {
            Capsule::connection()->beginTransaction();
            
            Capsule::schema()->table('mod_mymodule_data', function ($table) {
                $table->string('status', 50)->default('pending')->after('value');
            });
            
            // Verify
            if (!Capsule::schema()->hasColumn('mod_mymodule_data', 'status')) {
                throw new \Exception('Column "status" not added');
            }
            
            Capsule::connection()->commit();
            logActivity("MyModule: Upgraded to v1.1.0 — added status column.");
        } catch (\Exception $e) {
            Capsule::connection()->rollBack();
            logActivity("MyModule: Upgrade to v1.1.0 failed — " . $e->getMessage());
            throw $e;
        }
    }

    // Version 1.2.0: Add index and new table
    if (version_compare($currentVersion, '1.2.0', '<')) {
        try {
            Capsule::connection()->beginTransaction();
            
            Capsule::schema()->table('mod_mymodule_data', function ($table) {
                $table->index('status');
            });

            if (!Capsule::schema()->hasTable('mod_mymodule_audit')) {
                Capsule::schema()->create('mod_mymodule_audit', function ($table) {
                    $table->increments('id');
                    $table->unsignedInteger('record_id');
                    $table->string('action', 100);
                    $table->text('details')->nullable();
                    $table->timestamp('created_at')->useCurrent();
                });
            }
            
            Capsule::connection()->commit();
            logActivity("MyModule: Upgraded to v1.2.0 — added audit table.");
        } catch (\Exception $e) {
            Capsule::connection()->rollBack();
            logActivity("MyModule: Upgrade to v1.2.0 failed — " . $e->getMessage());
            throw $e;
        }
    }
}
```

### Rollback Pattern

```php
<?php

function mymodule_rollback(array $vars): void
{
    $currentVersion = $vars['version'];

    // Rollback from 1.2.0 to 1.1.0
    if (version_compare($currentVersion, '1.2.0', '>=')) {
        try {
            Capsule::connection()->beginTransaction();
            
            if (Capsule::schema()->hasTable('mod_mymodule_audit')) {
                Capsule::schema()->dropIfExists('mod_mymodule_audit');
            }
            
            Capsule::connection()->commit();
            logActivity("MyModule: Rolled back from v1.2.0");
        } catch (\Exception $e) {
            Capsule::connection()->rollBack();
            logActivity("MyModule: Rollback failed — " . $e->getMessage());
            throw $e;
        }
    }

    // Rollback from 1.1.0 to 1.0.0
    if (version_compare($currentVersion, '1.1.0', '>=')) {
        try {
            Capsule::connection()->beginTransaction();
            
            if (Capsule::schema()->hasColumn('mod_mymodule_data', 'status')) {
                Capsule::schema()->table('mod_mymodule_data', function ($table) {
                    $table->dropColumn('status');
                });
            }
            
            Capsule::connection()->commit();
            logActivity("MyModule: Rolled back from v1.1.0");
        } catch (\Exception $e) {
            Capsule::connection()->rollBack();
            logActivity("MyModule: Rollback failed — " . $e->getMessage());
            throw $e;
        }
    }
}
```

---

## 14. Common Pitfalls & Anti-Patterns

### 🔴 Critical Issues (Will Break Your Module)

| Pitfall                                  | Why It Breaks                                                    | Fix                                                              |
|------------------------------------------|------------------------------------------------------------------|------------------------------------------------------------------|
| Using `mysql_*` / `mysqli_*`             | Removed/deprecated; breaks on PHP 8+                             | Use `Capsule::table()`                                           |
| Using `{php}` in Smarty templates        | Removed in Smarty v4 (WHMCS 9.x)                                | Use Smarty tags or pass data via `vars`                          |
| Dynamic properties in classes            | Deprecated in PHP 8.2, error in 9.0                              | Declare all properties explicitly                                |
| Missing `defined("WHMCS")` guard         | Files become directly accessible via URL                         | Add guard as first line of every PHP file                        |
| Hardcoding file paths                    | Breaks across installations/environments                         | Use `ROOTDIR` and WHMCS constants                                |
| Modifying core files                     | Overwritten on WHMCS updates, causes corruption                  | Use Hooks and Modules instead                                    |
| Returning instead of echoing in `_output`| Admin output function must `echo`, not `return`                  | Use `echo` in addon `_output()`                                  |
| Echoing in `_clientarea`                 | Client area function must `return` an array                      | Return `['templatefile' => ..., 'vars' => ...]`                  |

### 🟡 Common Mistakes (Will Cause Bugs)

| Pitfall                                  | Issue                                                            | Fix                                                              |
|------------------------------------------|------------------------------------------------------------------|------------------------------------------------------------------|
| Not wrapping DB calls in try/catch       | Unhandled exceptions crash the admin area                        | Always use try/catch with Capsule operations                     |
| Forgetting `logModuleCall()`             | Impossible to debug API issues in production                     | Log every external API call                                      |
| Not scrubbing sensitive data from logs   | Passwords/keys visible in Module Log                             | Pass sensitive strings in `$replaceVars`                         |
| Raw SQL table names without `tbl` prefix | WHMCS core tables use `tbl` prefix (e.g., `tblclients`)         | Use exact table names including prefix                           |
| Adding hooks.php after module activation | WHMCS won't recognise new hooks file                             | Deactivate and reactivate module                                 |
| Using `$_REQUEST` instead of specific    | Security risk; ambiguous data source                             | Use `$_POST` or `$_GET` explicitly                               |
| Forgetting language files                | Module is not translatable; hard-coded strings                   | Create `lang/english.php` with all strings                       |
| Not using the `_upgrade` function        | Schema changes not applied when users update the module          | Use `version_compare()` in `_upgrade()`                          |
| Illuminate v7 patterns on WHMCS 9.x     | Fatal errors from incompatible Eloquent methods                  | Test queries specifically against Illuminate v9                  |
| Not validating callback signatures       | Gateway callbacks can be forged                                  | Always verify HMAC signatures                                    |
| Direct published invoice edits (9.x)     | Modifying published invoices directly breaks immutability law    | Use Credit/Debit Notes for published invoice adjustments        |

### 🟢 Best Practices (Will Make Users Love Your Module)

| Practice                                 | Benefit                                                          |
|------------------------------------------|------------------------------------------------------------------|
| Use WHMCS native Bootstrap classes       | Module UI looks native and professional                          |
| Include comprehensive language files     | Module is instantly translatable for international users         |
| Implement `TestConnection` (provisioning)| Admin can verify server connectivity before using the module     |
| Include breadcrumbs in client area       | Better navigation UX for clients                                 |
| Use `_upgrade` for all schema changes    | Seamless version upgrades without manual SQL                     |
| Bundle a README.md with the module       | Users know how to install and configure                          |
| Add an Admin Dashboard Widget            | Key metrics visible at a glance                                  |
| Support configurable options via _config | No code changes needed for different environments                |
| Write comprehensive tests                | Bugs caught early, easier maintenance                            |
| Use type declarations everywhere         | Fewer runtime errors, better IDE support                         |
| Implement proper logging                 | Easier debugging in production                                   |
| Cache expensive queries                  | Better performance for large datasets                            |

---

## 15. Project Structure Templates

### Addon Module Structure

```
/modules/addons/mymodule/
├── mymodule.php              # Main file: _config, _activate, _deactivate,
│                             #   _upgrade, _output, _clientarea
├── hooks.php                 # Action hooks (auto-loaded when module active)
├── lang/
│   └── english.php           # Language strings
├── lib/
│   ├── ApiClient.php         # External API client class
│   ├── Helper.php            # Utility functions
│   └── DataManager.php       # Database abstraction layer
├── templates/
│   ├── admin/
│   │   ├── dashboard.tpl     # Admin dashboard view
│   │   ├── settings.tpl      # Admin settings view
│   │   └── list.tpl          # Admin data listing view
│   └── client/
│       └── dashboard.tpl     # Client area dashboard view
├── tests/
│   ├── Unit/                 # Unit tests
│   │   ├── MyModuleTest.php
│   │   └── ApiClientTest.php
│   └── Integration/          # Integration tests
│       └── DatabaseTest.php
├── README.md                 # Installation & usage guide
├── CHANGELOG.md              # Version history
├── LICENSE                   # License file
└── composer.json             # Dependencies (if using Composer)
```

### Provisioning Module Structure

```
/modules/servers/myserver/
├── myserver.php              # Main file: all provisioning functions
├── hooks.php                 # Action hooks (optional)
├── lib/
│   ├── ApiClient.php         # Server API client class
│   └── Helper.php            # Utility functions
├── templates/
│   └── clientarea.tpl        # Client area service output
├── tests/
│   └── Unit/
│       └── MyServerTest.php
├── README.md
├── CHANGELOG.md
└── composer.json
```

### Registrar Module Structure

```
/modules/registrars/myregistrar/
├── myregistrar.php           # Main file: all registrar functions
├── hooks.php                 # Action hooks (optional)
├── lib/
│   ├── ApiClient.php         # Registrar API client class
│   └── DomainHelper.php      # Domain utility functions
├── tests/
│   └── Unit/
│       └── MyRegistrarTest.php
├── README.md
├── CHANGELOG.md
└── composer.json
```

### Payment Gateway Structure

```
/modules/gateways/
├── mygateway.php             # Main gateway file (_config, _link or _capture)
├── callback/
│   └── mygateway.php         # Callback handler for payment notifications

/modules/gateways/mygateway/  # (Optional) Library files
├── lib/
│   └── ApiClient.php
├── tests/
│   └── Unit/
│       └── MyGatewayTest.php
├── README.md
└── composer.json
```

---

## 16. Quick-Reference Code Snippets

### Module Security Guard

```php
<?php
// FIRST LINE of every PHP file in a module
defined("WHMCS") or die("Access Denied");
```

### Language File Template

```php
<?php
// /modules/addons/mymodule/lang/english.php

$_ADDONLANG['module_title']           = 'My Module';
$_ADDONLANG['dashboard_title']        = 'Dashboard';
$_ADDONLANG['settings_title']         = 'Settings';
$_ADDONLANG['client_page_title']      = 'My Module';
$_ADDONLANG['no_records_found']       = 'No records found.';
$_ADDONLANG['column_id']             = 'ID';
$_ADDONLANG['column_name']           = 'Name';
$_ADDONLANG['column_status']         = 'Status';
$_ADDONLANG['btn_save']              = 'Save Changes';
$_ADDONLANG['btn_cancel']            = 'Cancel';
$_ADDONLANG['success_saved']         = 'Settings saved successfully.';
$_ADDONLANG['error_generic']         = 'An error occurred. Please try again.';
$_ADDONLANG['error_required']        = 'This field is required.';
$_ADDONLANG['error_invalid_email']   = 'Please enter a valid email address.';
$_ADDONLANG['error_invalid_url']     = 'Please enter a valid URL.';
```

### Capsule Check If Table Exists

```php
<?php

use Illuminate\Database\Capsule\Manager as Capsule;

if (Capsule::schema()->hasTable('mod_mymodule_data')) {
    // Table exists
}

if (Capsule::schema()->hasColumn('mod_mymodule_data', 'status')) {
    // Column exists
}
```

### Fetch Admin User in Module

```php
<?php

// Inside _output() — get current admin user ID
$adminId = $_SESSION['adminid'];
$admin = Capsule::table('tbladmins')->where('id', $adminId)->first();
```

### Client Area URL Routing

```php
<?php

// Client area modules are accessed at:
// https://yourdomain.com/index.php?m=mymodule
// https://yourdomain.com/index.php?m=mymodule&action=settings

function mymodule_clientarea($vars): array
{
    $action = $_GET['action'] ?? 'index';

    switch ($action) {
        case 'settings':
            return [
                'pagetitle'    => 'Settings',
                'templatefile' => 'templates/client/settings',
                'requirelogin' => true,
                'vars'         => ['currentSettings' => $settings],
            ];

        default:
            return [
                'pagetitle'    => 'Dashboard',
                'templatefile' => 'templates/client/dashboard',
                'requirelogin' => true,
                'vars'         => ['data' => $data],
            ];
    }
}
```

### Hook — Add Menu Entry

```php
<?php

add_hook('ClientAreaPrimarySidebar', 1, function ($sidebar) {
    /** @var \WHMCS\View\Menu\Item $sidebar */
    $myPanel = $sidebar->addChild('myModulePanel', [
        'label' => 'My Module',
        'uri'   => 'index.php?m=mymodule',
        'order' => 99,
        'icon'  => 'fas fa-cog',
    ]);

    $myPanel->addChild('dashboard', [
        'label' => 'Dashboard',
        'uri'   => 'index.php?m=mymodule',
        'order' => 1,
    ]);

    $myPanel->addChild('settings', [
        'label' => 'Settings',
        'uri'   => 'index.php?m=mymodule&action=settings',
        'order' => 2,
    ]);
});
```

### Hook — Modify Checkout

```php
<?php

add_hook('ShoppingCartValidateCheckout', 1, function (array $vars) {
    $errors = [];

    // Example: require a custom field to be filled
    if (empty($_POST['customfield_company'])) {
        $errors[] = 'Company name is required for checkout.';
    }

    // Return errors to block checkout, or empty array to proceed
    return $errors;
});
```

### WHMCS Encryption Helpers

```php
<?php

// Encrypt sensitive data before storing
$encrypted = encrypt('my-secret-api-key');
Capsule::table('mod_mymodule_settings')
    ->insert(['key' => 'api_key', 'value' => $encrypted]);

// Decrypt when reading
$row = Capsule::table('mod_mymodule_settings')
    ->where('key', '=', 'api_key')
    ->first();
$apiKey = decrypt($row->value);
```

---

## 17. Development Environment Setup

### Local WHMCS Installation with Docker

```bash
# Create docker-compose.yml
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  whmcs:
    image: php:8.2-apache
    container_name: whmcs-dev
    ports:
      - "8080:80"
    volumes:
      - ./whmcs:/var/www/html
      - ./php.ini:/usr/local/etc/php/conf.d/custom.ini
    environment:
      - MYSQL_HOST=db
      - MYSQL_DATABASE=whmcs
      - MYSQL_USER=whmcs
      - MYSQL_PASSWORD=whmcs
    depends_on:
      - db

  db:
    image: mysql:8.0
    container_name: whmcs-db
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=whmcs
      - MYSQL_USER=whmcs
      - MYSQL_PASSWORD=whmcs
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - "3306:3306"

volumes:
  db_data:
EOF

# Start development environment
docker-compose up -d

# Install WHMCS
# Download WHMCS from https://www.whmcs.com/download/
# Extract to ./whmcs directory
# Visit http://localhost:8080 to complete installation
```

### Debugging Configuration

```php
<?php

// Enable debug mode in configuration.php
$config['debug'] = true;
$config['display_errors'] = true;
$config['error_reporting'] = E_ALL;

// Log errors to file
$config['log_errors'] = true;
$config['error_log'] = '/path/to/whmcs/logs/php-error.log';
```

### Hot Reload for Template Development

```bash
# Install Node.js dependencies for template compilation
npm install -g sass
npm install -g live-server

# Watch for template changes
sass --watch templates/scss:templates/css

# Live reload browser
live-server --port=3000 --open=templates/
```

---

## 18. Code Quality & Review Guidelines

### PHPStan Integration

```bash
# Install PHPStan
composer require --dev phpstan/phpstan:^1.10

# Install WHMCS Stubs to prevent undefined symbol warnings
composer require --dev krystal/whmcs-stubs

# Create phpstan.neon
cat > phpstan.neon << 'EOF'
parameters:
    level: 6
    paths:
        - modules/addons/mymodule
        - modules/servers/myserver
    bootstrapFiles:
        - tests/bootstrap.php # Loads mock definitions of WHMCS functions
    ignoreErrors:
        - '#Call to an undefined method#'
EOF

# Run analysis
vendor/bin/phpstan analyse
```

### Code Review Checklist

```markdown
## Security
- [ ] All user input is validated and sanitized
- [ ] Database queries use parameterized queries (Capsule)
- [ ] Sensitive data is encrypted (encrypt()/decrypt())
- [ ] HMAC signatures are verified for callbacks
- [ ] No hardcoded credentials or secrets

## Code Quality
- [ ] All functions have return type declarations
- [ ] All parameters have type hints
- [ ] PSR-12 coding standards are followed
- [ ] No dynamic properties on classes
- [ ] Proper error handling with try/catch

## Performance
- [ ] Database queries are optimized (no SELECT *)
- [ ] Appropriate indexes are added
- [ ] Expensive queries are cached
- [ ] N+1 query problems are avoided

## Testing
- [ ] Unit tests cover all public methods
- [ ] Integration tests cover database operations
- [ ] Edge cases are tested
- [ ] Tests pass without errors

## Documentation
- [ ] README.md is updated
- [ ] CHANGELOG.md is updated
- [ ] Code comments explain complex logic
- [ ] Language files are complete
```

---

## 19. Internationalization (i18n)

### Multi-Language Support

```php
<?php

// Language file structure
// /modules/addons/mymodule/lang/english.php
$_ADDONLANG['module_title'] = 'My Module';

// /modules/addons/mymodule/lang/bangla.php
$_ADDONLANG['module_title'] = 'আমার মডিউল';

// /modules/addons/mymodule/lang/arabic.php
$_ADDONLANG['module_title'] = 'الوحدة الخاصة بي';

// Detect client language
function getModuleLanguage(): string {
    $clientLang = $_SESSION['language'] ?? 'english';
    $langFile = __DIR__ . '/lang/' . $clientLang . '.php';
    
    if (file_exists($langFile)) {
        return $clientLang;
    }
    
    return 'english'; // Fallback
}
```

### RTL Language Support

```php
<?php

// Check if language is RTL
function isRtlLanguage(string $language): bool {
    $rtlLanguages = ['arabic', 'hebrew', 'persian', 'urdu'];
    return in_array(strtolower($language), $rtlLanguages);
}

// Add RTL class to body
if (isRtlLanguage($currentLanguage)) {
    $bodyClass = 'rtl';
}
```

### Date/Number Formatting

```php
<?php

function formatModuleDate(string $date, string $format = 'Y-m-d'): string {
    $timestamp = strtotime($date);
    return date($format, $timestamp);
}

function formatModuleNumber(float $number, int $decimals = 2): string {
    return number_format($number, $decimals, '.', ',');
}

function formatModuleCurrency(float $amount, string $currency = 'USD'): string {
    $symbols = [
        'USD' => '$',
        'EUR' => '€',
        'GBP' => '£',
        'BDT' => '৳',
    ];
    
    $symbol = $symbols[$currency] ?? $currency;
    return $symbol . formatModuleNumber($amount);
}
```

---

## 20. Deployment & CI/CD

### GitHub Actions Workflow

```yaml
# .github/workflows/ci.yml
name: WHMCS Module CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.2'
        extensions: mbstring, xml, ctype, json, bcmath
        coverage: xdebug
        
    - name: Install Dependencies
      run: composer install --prefer-dist --no-progress
      
    - name: Run Tests
      run: vendor/bin/phpunit --coverage-text
      
    - name: Run PHPStan
      run: vendor/bin/phpstan analyse
      
    - name: Check Coding Standards
      run: vendor/bin/phpcs --standard=PSR12 modules/
```

### Deployment Script

```bash
#!/bin/bash

# deploy.sh — Deploy WHMCS module to production

set -e

MODULE_NAME="mymodule"
REMOTE_HOST="production.example.com"
REMOTE_PATH="/var/www/html/modules/addons/$MODULE_NAME"
BACKUP_PATH="/backup/modules/$MODULE_NAME_$(date +%Y%m%d_%H%M%S)"

echo "Starting deployment of $MODULE_NAME..."

# Create backup
echo "Creating backup..."
ssh $REMOTE_HOST "mkdir -p $BACKUP_PATH && cp -r $REMOTE_PATH/* $BACKUP_PATH/"

# Deploy files
echo "Deploying files..."
rsync -avz --exclude='.git' --exclude='tests' --exclude='vendor' \
  ./modules/addons/$MODULE_NAME/ $REMOTE_HOST:$REMOTE_PATH/

# Set permissions
echo "Setting permissions..."
ssh $REMOTE_HOST "find $REMOTE_PATH -type f -exec chmod 644 {} \; && \
                  find $REMOTE_PATH -type d -exec chmod 755 {} \;"

# Clear WHMCS templates cache
echo "Clearing templates_c cache..."
ssh $REMOTE_HOST "find /var/www/html/templates_c/ -type f ! -name '.htaccess' ! -name 'index.php' -delete"

# Note: WHMCS handles database migrations internally via the module's _upgrade() hook
# upon the first admin access after the module files are updated on the remote host.

echo "Deployment complete!"
```

### Environment-Specific Configuration

```php
<?php

// config/environment.php
function getEnvironmentConfig(): array {
    $env = getenv('WHMCS_ENV') ?: 'production';
    
    $configs = [
        'development' => [
            'debug' => true,
            'display_errors' => true,
            'log_level' => 'debug',
        ],
        'staging' => [
            'debug' => true,
            'display_errors' => false,
            'log_level' => 'info',
        ],
        'production' => [
            'debug' => false,
            'display_errors' => false,
            'log_level' => 'error',
        ],
    ];
    
    return $configs[$env] ?? $configs['production'];
}
```

---

## 21. Real-World Examples

### Example 1: Multi-Tenant Architecture

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

use Illuminate\Database\Capsule\Manager as Capsule;

class MultiTenantModule {
    private int $tenantId;
    private array $config;

    public function __construct(int $tenantId) {
        $this->tenantId = $tenantId;
        $this->config = $this->loadTenantConfig();
    }

    private function loadTenantConfig(): array {
        $config = Capsule::table('mod_mymodule_tenants')
            ->where('id', $this->tenantId)
            ->first();
        return $config ? (array) $config : [];
    }

    public function getClientData(int $clientId): array {
        return Capsule::table('mod_mymodule_data')
            ->where('tenant_id', $this->tenantId)
            ->where('client_id', $clientId)
            ->get()
            ->toArray();
    }

    public function syncWithExternalApi(): bool {
        try {
            $data = $this->fetchFromApi();
            $this->storeData($data);
            return true;
        } catch (\Exception $e) {
            logActivity("MultiTenant Sync Error: " . $e->getMessage());
            return false;
        }
    }
}

// Usage
$module = new MultiTenantModule($tenantId);
$clientData = $module->getClientData($clientId);
```

### Example 2: Complex Provisioning with Webhooks

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function myserver_CreateAccount(array $params): string {
    try {
        // Create account on remote server
        $response = myserver_apiCall('createAccount', [
            'domain' => $params['domain'],
            'username' => $params['username'],
            'password' => $params['password'],
            'package' => $params['configoption1'],
        ]);

        if (!$response['success']) {
            return 'Error: ' . $response['message'];
        }

        // Store webhook URL for status updates
        $webhookUrl = $params['systemurl'] . 'modules/servers/myserver/webhook.php';
        
        myserver_apiCall('setWebhook', [
            'account_id' => $response['account_id'],
            'webhook_url' => $webhookUrl,
            'events' => ['suspended', 'terminated', 'renewed'],
        ]);

        logActivity("MyServer: Account {$params['domain']} provisioned with webhook");
        return 'success';

    } catch (\Exception $e) {
        logModuleCall('myserver', 'CreateAccount', $params, $e->getMessage());
        return 'Error: ' . $e->getMessage();
    }
}

// Webhook handler
// /modules/servers/myserver/webhook.php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $payload = json_decode(file_get_contents('php://input'), true);
    
    // Verify webhook signature
    $signature = $_SERVER['HTTP_X_WEBHOOK_SIGNATURE'] ?? '';
    if (!verifyWebhookSignature($payload, $signature)) {
        http_response_code(401);
        die('Invalid signature');
    }
    
    // Process webhook
    $accountId = $payload['account_id'];
    $event = $payload['event'];
    
    switch ($event) {
        case 'suspended':
            // Handle suspension
            break;
        case 'terminated':
            // Handle termination
            break;
    }
}
```

### Example 3: Payment Gateway with Idempotency

```php
<?php

declare(strict_types=1);

defined("WHMCS") or die("Access Denied");

function mygateway_capture(array $params): array {
    $invoiceId = $params['invoiceid'];
    $amount = $params['amount'];
    
    // Generate idempotency key
    $idempotencyKey = 'payment_' . $invoiceId . '_' . md5($amount . $invoiceId);
    
    // Check if payment already processed
    $existingPayment = Capsule::table('mod_gateway_payments')
        ->where('idempotency_key', $idempotencyKey)
        ->first();
    
    if ($existingPayment) {
        // Return existing result
        return [
            'status' => $existingPayment->status,
            'transid' => $existingPayment->transaction_id,
        ];
    }
    
    try {
        // Process payment
        $response = mygateway_apiCall('charge', [
            'amount' => $amount,
            'currency' => $params['currency'],
            'card' => $params['cardnum'],
            'idempotency_key' => $idempotencyKey,
        ]);
        
        // Store payment record
        Capsule::table('mod_gateway_payments')->insert([
            'idempotency_key' => $idempotencyKey,
            'invoice_id' => $invoiceId,
            'amount' => $amount,
            'status' => $response['status'],
            'transaction_id' => $response['transaction_id'] ?? null,
            'created_at' => date('Y-m-d H:i:s'),
        ]);
        
        if ($response['status'] === 'approved') {
            return [
                'status' => 'success',
                'transid' => $response['transaction_id'],
            ];
        }
        
        return ['status' => 'declined'];
        
    } catch (\Exception $e) {
        logModuleCall('mygateway', 'capture', $params, $e->getMessage());
        return ['status' => 'error', 'rawdata' => $e->getMessage()];
    }
}
```

---

## 22. Official References

| Resource                            | URL                                                          |
|-------------------------------------|--------------------------------------------------------------|
| **Developer Documentation (Home)**  | <https://developers.whmcs.com/>                               |
| **API Reference**                   | <https://developers.whmcs.com/api-reference/>                 |
| **API Index**                       | <https://developers.whmcs.com/api/api-index/>                 |
| **Hook Reference**                  | <https://developers.whmcs.com/hooks-reference/>               |
| **Hook Index**                      | <https://developers.whmcs.com/hooks/hook-index/>              |
| **Classes Reference**               | <https://docs.whmcs.com/classes/>                             |
| **Style Guide**                     | <https://developers.whmcs.com/modules/style-guide/>           |
| **Sample Addon Module**             | <https://github.com/WHMCS/sample-addon-module>                |
| **Sample Provisioning Module**      | <https://github.com/WHMCS/sample-provisioning-module>         |
| **Sample Registrar Module**         | <https://github.com/WHMCS/sample-registrar-module>            |
| **Sample Gateway Module**           | <https://github.com/WHMCS/sample-gateway-module>              |
| **Sample Merchant Gateway**         | <https://github.com/WHMCS/sample-merchant-gateway>            |
| **WHMCS Community Forums**          | <https://whmcs.community/>                                    |
| **Beta API Documentation**          | <https://api-beta.developers.whmcs.com/>                      |
| **PHP Documentation**               | <https://www.php.net/docs.php>                                |
| **Smarty Documentation**            | <https://www.smarty.net/docs/>                                |
| **Laravel Capsule**                 | <https://laravel.com/docs/eloquent>                           |
| **PHPUnit**                         | <https://phpunit.de/>                                         |
| **PSR-12 Coding Style**            | <https://www.php-fig.org/psr/psr-12/>                         |

---

## License

This skill is licensed under the [MIT License](LICENSE).

---

## Author

### Niloy

- 🌐 Website: [niloy.io](https://niloy.io)
- 🐙 GitHub: [@beingniloy](https://github.com/beingniloy)
- 📦 Skill: `whmcs-dev-skill`

---

> *Built with ❤️ for the WHMCS developer community.*
> *Researched from official WHMCS Developer Documentation, common issue reports,*
> *and community best practices (2024–2025).*
>
> *This is the enhanced v2.0 with testing, security hardening, performance*
> *optimization, and production-ready deployment patterns.*

---
name: security-audit
description: Audit a Drupal site for security issues and vulnerabilities
arguments: "[path]"
---

# Drupal Security Audit

Perform a comprehensive security audit of the Drupal site.

**Usage:** `/security-audit [path]`

If a path is provided, focus the audit on that module or directory. Otherwise, audit the entire site.

## Audit Steps

### 1. Check Module Security Updates

```bash
drush pm:security
```

Review any modules with known security vulnerabilities. **These should be updated immediately.**

### 2. Check All Outdated Modules

```bash
composer outdated drupal/*
```

Outdated modules may have unpatched vulnerabilities even without security advisories.

### 3. Review User Permissions

```bash
drush role:list
drush user:role:list --roles=administrator
```

Check for:
- Users with administrator role who shouldn't have it
- Overly permissive anonymous/authenticated user permissions
- Unused roles that should be removed

### 4. Check File Permissions

```bash
# Check settings.php is not writable
ls -la web/sites/default/settings.php

# Check files directory permissions
ls -la web/sites/default/files/

# Look for PHP files in files directory (should be NONE)
find web/sites/default/files -name "*.php" -type f
```

**Expected:**
- `settings.php` should be 444 or 644 (not writable)
- `files/` directory should not contain any `.php` files
- `.htaccess` should exist in files directory

### 5. Check for Exposed Sensitive Files

```bash
# These should NOT be web-accessible
ls -la .env 2>/dev/null
ls -la .git 2>/dev/null
ls -la composer.json
ls -la web/.htaccess
```

Verify `.htaccess` or server config blocks access to:
- `.env` files
- `.git` directory
- `composer.json` / `composer.lock`
- `vendor/` directory
- `config/sync/` directory

### 6. Review Custom Code for Common Vulnerabilities

Scan custom modules for security issues:

```bash
# Find potential SQL injection (string concatenation in queries)
grep -r "db_query.*\\\$" modules/custom/
grep -r '->query.*".*\$' modules/custom/

# Find potential XSS (raw output without sanitization)
grep -r "#markup.*\\\$" modules/custom/
grep -r "Markup::create.*\\\$" modules/custom/

# Find hardcoded credentials
grep -ri "password\s*=" modules/custom/
grep -ri "api_key\s*=" modules/custom/
grep -ri "secret" modules/custom/

# Find static service calls (testability/security smell)
grep -r "\\\\Drupal::" modules/custom/src/
```

### 7. Check Drupal Security Settings

```bash
drush config:get system.site page.403
drush config:get system.site page.404
drush config:get system.performance css.preprocess
drush config:get system.performance js.preprocess
```

Production sites should have:
- Custom 403/404 pages (not default Drupal error pages)
- CSS/JS aggregation enabled

### 8. Review Trusted Host Patterns

```bash
drush php:eval "print_r(\$settings['trusted_host_patterns'] ?? 'NOT SET');"
```

**This MUST be set in production** to prevent host header attacks.

### 9. Check for Debug Mode

```bash
# These should be FALSE in production
drush php:eval "var_dump(\$config['system.logging']['error_level']);"
drush php:eval "var_dump(\$settings['twig_debug'] ?? false);"
```

Production settings:
- `error_level` should be `hide` (not `verbose` or `all`)
- `twig_debug` should be `false`

### 10. Database Security

```bash
# Check for direct database access attempts in logs
drush watchdog:show --filter="type=php" --count=20
```

## Security Report Format

Organize findings by severity:

### CRITICAL (Fix Immediately)
- Security updates available
- SQL injection vulnerabilities
- Exposed credentials
- Missing trusted_host_patterns in production

### HIGH (Fix Soon)
- XSS vulnerabilities
- Overly permissive file permissions
- PHP files in files directory
- Debug mode enabled in production

### MEDIUM (Should Address)
- Outdated modules without security advisories
- Static service calls in custom code
- Missing custom error pages

### LOW (Consider)
- Minor coding standard issues
- Unused permissions or roles

## Automated Scanning Tools

For deeper analysis, recommend:

```bash
# Drupal Security Review module
drush en security_review
drush security:review

# PHP CodeSniffer with security sniffs
./vendor/bin/phpcs --standard=Drupal,DrupalPractice modules/custom/

# Composer audit
composer audit
```

## Post-Audit Actions

1. **Immediate:** Apply all security updates
2. **Short-term:** Fix critical/high issues
3. **Ongoing:** Schedule regular security reviews
4. **Document:** Record findings and remediation timeline

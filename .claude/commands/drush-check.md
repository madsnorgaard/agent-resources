---
name: drush-check
description: Run common Drush checks to verify Drupal site health
---

# Drush Health Check

Run a series of Drush commands to check site health and status.

## Steps

1. Check Drush is available:
   ```bash
   drush status
   ```

2. Check for available updates:
   ```bash
   drush pm:security
   ```

3. Check configuration status:
   ```bash
   drush config:status
   ```

4. Check for pending database updates:
   ```bash
   drush updatedb:status
   ```

5. Check watchdog for recent errors:
   ```bash
   drush watchdog:show --severity=error --count=10
   ```

## Report Findings

Summarize:
- Drupal version and status
- Any security updates needed
- Configuration sync status
- Pending database updates
- Recent errors in logs

---
name: config-export
description: Export Drupal configuration with proper workflow
---

# Drupal Configuration Export

Safely export configuration changes with proper review.

## Workflow

1. **Check current status**
   ```bash
   drush config:status
   ```

2. **Review what will be exported**
   Show the user what configurations have changed and let them confirm.

3. **Export configuration**
   ```bash
   drush config:export -y
   ```

4. **Review exported files**
   ```bash
   git status config/sync/
   git diff config/sync/
   ```

5. **Stage for commit**
   After user reviews, stage the config changes:
   ```bash
   git add config/sync/
   ```

## Important Notes

- Never auto-commit without user confirmation
- Review UUIDs - they should match across environments
- Check for sensitive data in exported config
- Verify config schema exists for custom config

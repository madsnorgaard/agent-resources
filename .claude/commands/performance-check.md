---
name: performance-check
description: Analyze Drupal site performance and caching configuration
arguments: "[module_path]"
---

# Drupal Performance Check

Analyze site performance configuration and identify optimization opportunities.

**Usage:** `/performance-check [module_path]`

If a module path is provided, focus analysis on that module's code. Otherwise, check site-wide performance settings and scan all custom modules.

## Performance Analysis Steps

### 1. Check Caching Configuration

```bash
# Page cache
drush config:get system.performance cache.page.max_age

# CSS/JS aggregation
drush config:get system.performance css.preprocess
drush config:get system.performance js.preprocess

# Twig cache (should NOT be disabled in production)
drush php:eval "var_dump(\$settings['cache']['bins']['render'] ?? 'default');"
```

**Production should have:**
- `cache.page.max_age` > 0 (e.g., 3600 for 1 hour)
- `css.preprocess` = true
- `js.preprocess` = true
- No cache bins disabled

### 2. Check Cache Backends

```bash
# What cache backends are configured?
drush php:eval "print_r(\$settings['cache']['default'] ?? 'database');"

# List all cache bins
drush cache:list
```

**Recommendations:**
- Use Redis or Memcache for production (not database)
- Consider separate backends for different cache bins

### 3. Review BigPipe Status

```bash
drush pm:list --filter=big_pipe
drush config:get big_pipe.settings
```

BigPipe should be enabled for authenticated user performance.

### 4. Check Database Performance

```bash
# Show slow query log (if enabled)
drush watchdog:show --filter="type=php" --severity=warning --count=20

# Check database size
drush sql:query "SELECT table_name, round(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)' FROM information_schema.tables WHERE table_schema = DATABASE() ORDER BY (data_length + index_length) DESC LIMIT 10;"
```

Look for:
- Large cache tables (may need pruning)
- Large watchdog table (configure log rotation)
- Orphaned data

### 5. Analyze Cache Metadata in Custom Code

Search for missing cache metadata:

```bash
# Find render arrays without cache metadata
grep -r "#markup" modules/custom/ | grep -v "#cache"

# Find entity queries that might need caching
grep -r "getQuery" modules/custom/
grep -r "entityQuery" modules/custom/
```

Every render array should include `#cache` with appropriate:
- `tags` - What data does this depend on?
- `contexts` - What varies this output? (user, url, permissions)
- `max-age` - How long is this valid?

### 6. Check for N+1 Query Patterns

```bash
# Find potential N+1 patterns (loading entities in loops)
grep -r "->load(" modules/custom/ | head -20
grep -r "entity_load" modules/custom/ | head -20

# Find queries inside foreach
grep -rB5 "->load(" modules/custom/ | grep -A5 "foreach"
```

**Fix N+1 patterns:**
- Use `loadMultiple()` instead of `load()` in loops
- Use entity reference fields with proper loading
- Pre-load entities before iteration

### 7. Views Performance

```bash
# List all views
drush views:list

# Check views with lots of results
drush views:analyze
```

Common views issues:
- Missing caching on views
- No pagination on large result sets
- Inefficient filters

### 8. Check for Static Caching

```bash
# Services without caching expensive operations
grep -r "public function get" modules/custom/src/Service/ | head -20
```

Expensive operations (API calls, complex queries) should use static caching:

```php
protected array $cache = [];

public function getExpensiveData(string $id): array {
  if (!isset($this->cache[$id])) {
    $this->cache[$id] = $this->computeExpensiveData($id);
  }
  return $this->cache[$id];
}
```

### 9. Check Cron and Queue Performance

```bash
# Last cron run
drush core:cron --verbose 2>&1 | head -5

# Queue status
drush queue:list
```

For heavy processing:
- Use Queue API instead of cron
- Process queues with separate workers
- Consider ultimate_cron for better cron management

### 10. Frontend Performance

```bash
# Check library definitions
find modules/custom -name "*.libraries.yml" -exec cat {} \;

# Look for inline JS/CSS (should use libraries)
grep -r "<script" modules/custom/templates/
grep -r "<style" modules/custom/templates/
```

Best practices:
- All JS/CSS via libraries.yml (enables aggregation)
- Defer non-critical JS
- Use critical CSS for above-the-fold

## Performance Report Format

### Critical Issues
- Page cache disabled
- CSS/JS aggregation off in production
- Database cache backend in production
- N+1 query patterns

### Warnings
- Missing cache metadata on render arrays
- Views without caching
- Large database tables
- Inline JS/CSS

### Optimization Opportunities
- Consider Redis/Memcache
- Implement lazy builders for personalized content
- Add static caching to expensive services
- Move heavy processing to queues

## Quick Wins Checklist

```bash
# Enable all standard performance settings
drush config:set system.performance cache.page.max_age 3600
drush config:set system.performance css.preprocess true
drush config:set system.performance js.preprocess true
drush en big_pipe
drush cache:rebuild
```

## Monitoring Recommendations

For ongoing performance monitoring:
- **New Relic** or **Blackfire** for APM
- **Grafana + Prometheus** for metrics
- **drush pm:security** in CI for update alerts
- Regular database maintenance (optimize tables)

## Further Reading

- [Drupal Caching Guide](https://www.drupal.org/docs/drupal-apis/cache-api)
- [BigPipe Module](https://www.drupal.org/docs/core-modules-and-themes/core-modules/bigpipe-module)
- [Performance Best Practices](https://www.drupal.org/docs/administering-a-drupal-site/performance-and-caching)

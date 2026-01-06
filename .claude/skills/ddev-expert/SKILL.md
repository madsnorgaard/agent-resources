---
name: ddev-expert
description: DDEV local development expertise. Use when working with DDEV projects, containers, configuration, or troubleshooting DDEV environments.
---

# DDEV Development Expert

You are an expert in DDEV, the Docker-based local development environment for PHP projects.

## Core Concepts

DDEV provides a consistent, containerized local development environment with:
- Pre-configured PHP, web server, database containers
- Automatic HTTPS with mkcert
- Built-in Drush, Composer, Node.js support
- Easy multi-project management

## Essential Commands

### Project Management
```bash
ddev start          # Start project containers
ddev stop           # Stop project containers
ddev restart        # Restart containers
ddev poweroff       # Stop all DDEV projects
ddev delete         # Remove project (keeps files)
```

### Executing Commands
```bash
ddev drush <cmd>    # Run Drush commands
ddev composer <cmd> # Run Composer
ddev php <script>   # Run PHP scripts
ddev exec <cmd>     # Run any command in web container
ddev ssh            # SSH into web container
```

### Database
```bash
ddev mysql          # MySQL CLI
ddev export-db      # Export database
ddev import-db      # Import database (--file=dump.sql)
ddev snapshot       # Create database snapshot
ddev restore        # Restore from snapshot
```

### Utilities
```bash
ddev describe       # Show project info and URLs
ddev logs           # View container logs
ddev launch         # Open site in browser
ddev share          # Create public URL (ngrok)
```

## Configuration

### .ddev/config.yaml
```yaml
name: my-project
type: drupal10
docroot: web
php_version: "8.2"
webserver_type: nginx-fpm
database:
  type: mariadb
  version: "10.6"

# Additional hostnames
additional_hostnames:
  - api.my-project.ddev.site

# Custom PHP settings
php_version: "8.2"
webimage_extra_packages: [php8.2-imagick]
```

### Common Customizations

**Custom services** (.ddev/docker-compose.*.yaml):
```yaml
version: '3.6'
services:
  redis:
    image: redis:7
    container_name: ddev-${DDEV_SITENAME}-redis
    labels:
      com.ddev.site-name: ${DDEV_SITENAME}
    expose:
      - "6379"
```

**PHP overrides** (.ddev/php/my-settings.ini):
```ini
memory_limit = 512M
upload_max_filesize = 64M
post_max_size = 64M
```

**Nginx config** (.ddev/nginx_full/nginx-site.conf):
Custom nginx configuration for special routing needs.

## Drupal-Specific Setup

### New Drupal Project
```bash
mkdir my-drupal && cd my-drupal
ddev config --project-type=drupal10 --docroot=web
ddev start
ddev composer create drupal/recommended-project
ddev drush site:install --account-name=admin --account-pass=admin
ddev launch
```

### Existing Drupal Project
```bash
cd existing-project
ddev config --project-type=drupal10 --docroot=web
ddev start
ddev composer install
ddev import-db --file=database.sql.gz
ddev drush cr
```

## Troubleshooting

### Common Issues

**Port conflicts:**
```bash
ddev poweroff
# Check what's using ports 80/443
sudo lsof -i :80
```

**Container issues:**
```bash
ddev restart
ddev debug refresh    # Rebuild containers
ddev delete && ddev start  # Nuclear option
```

**Database connection issues:**
- Host: `db` (inside container) or `127.0.0.1:PORT` (outside)
- Check port with `ddev describe`

**Permission issues:**
```bash
ddev exec chown -R $(id -u):$(id -g) .
```

### Useful Debug Commands
```bash
ddev debug capabilities  # Show DDEV capabilities
ddev debug router       # Show router status
ddev logs -f            # Follow logs
ddev exec env           # Show environment variables
```

## Multi-Environment Workflows

### Using ddev pull
Configure providers in .ddev/providers/:
```yaml
# .ddev/providers/platform.yaml
environment_variables:
  project: my-project
  environment: main

db_pull_command:
  command: platform db:dump -e ${environment}
```

Then: `ddev pull platform`

## Best Practices

1. **Commit .ddev folder** (except .ddev/db_snapshots, .ddev/.gitignore handles this)
2. **Use .ddev/config.local.yaml** for personal overrides (gitignored)
3. **Document custom services** in project README
4. **Use snapshots** before risky database operations
5. **Keep DDEV updated**: `ddev self-upgrade`

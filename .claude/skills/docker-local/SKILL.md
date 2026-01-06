---
name: docker-local
description: Custom Docker Compose local development patterns. Use when working with Docker-based local environments, container configuration, or troubleshooting Docker setups.
---

# Docker Compose Local Development

You are working with custom Docker Compose local development environments.

## Environment Awareness

When working on a project with Docker:
1. First check for `docker-compose.yml` or `compose.yml` in project root
2. Look for `.env` or `.env.local` for environment variables
3. Check for `Makefile` or shell scripts that wrap Docker commands
4. Note any `docker/` directory with custom Dockerfiles

## Common Patterns

### Starting the Environment
```bash
# Standard
docker compose up -d

# With build
docker compose up -d --build

# Specific services
docker compose up -d web db
```

### Executing Commands
```bash
# Run command in running container
docker compose exec <service> <command>

# Examples
docker compose exec php composer install
docker compose exec php drush cr
docker compose exec php bash

# Run in new container (useful if container isn't running)
docker compose run --rm php composer install
```

### Logs and Debugging
```bash
docker compose logs -f           # Follow all logs
docker compose logs -f php       # Follow specific service
docker compose ps                # Show running containers
docker compose top               # Show processes
```

### Cleanup
```bash
docker compose down              # Stop and remove containers
docker compose down -v           # Also remove volumes (DATABASE LOSS!)
docker compose rm -f             # Remove stopped containers
```

## Project-Specific Notes

<!--
Add your company/project-specific patterns here as you discover them:

### Project A Pattern
- Start command: make up
- PHP container: app
- Database: mysql on port 3307

### Project B Pattern
- Start command: ./scripts/start.sh
- PHP container: php-fpm
- Database: postgres on port 5432

### Common Issues
- Container X conflicts with Y when both running
- Need to stop project A before starting project B due to port 80
-->

## Drupal in Docker

### Common Container Names
Check your project's compose file, but common patterns:
- PHP/Web: `php`, `web`, `app`, `drupal`, `php-fpm`
- Database: `db`, `mysql`, `mariadb`, `postgres`
- Cache: `redis`, `memcached`

### Running Drush
```bash
# Find the right container name first
docker compose ps

# Then run drush
docker compose exec <php-container> drush <command>
docker compose exec <php-container> ./vendor/bin/drush <command>
```

### Database Access
```bash
# MySQL/MariaDB
docker compose exec db mysql -u root -p

# Import database
docker compose exec -T db mysql -u root -p<password> <dbname> < dump.sql

# Export database
docker compose exec db mysqldump -u root -p<password> <dbname> > dump.sql
```

## Troubleshooting

### Port Conflicts
```bash
# Find what's using a port
sudo lsof -i :80
sudo lsof -i :3306

# Or check all docker ports
docker ps --format "table {{.Names}}\t{{.Ports}}"
```

### Container Won't Start
```bash
# Check logs
docker compose logs <service>

# Rebuild from scratch
docker compose down
docker compose build --no-cache
docker compose up -d
```

### Permission Issues
```bash
# Check user in container
docker compose exec <service> id

# Fix permissions (if needed)
docker compose exec <service> chown -R www-data:www-data /var/www/html
```

### Network Issues
```bash
# List networks
docker network ls

# Inspect network
docker network inspect <project>_default

# Containers can reach each other by service name
# e.g., from php container: mysql -h db -u root
```

## Environment Variables

### .env File
Docker Compose automatically loads `.env` from project root:
```env
COMPOSE_PROJECT_NAME=myproject
PHP_VERSION=8.2
MYSQL_ROOT_PASSWORD=root
```

### In docker-compose.yml
```yaml
services:
  php:
    environment:
      - PHP_MEMORY_LIMIT=512M
    env_file:
      - .env.local
```

## Tips

1. **Use Makefile** - Wrap complex commands for team consistency
2. **Document container names** - Each project may differ
3. **Check ports before starting** - Avoid conflicts with other projects
4. **Use docker compose profiles** - For optional services
5. **Volume for vendor** - Speed up composer in containers

## Adding Project Patterns

As you work with different projects, add their patterns above in the "Project-Specific Notes" section. Include:
- How to start/stop
- Container names for PHP, DB
- Any custom scripts or make targets
- Known conflicts with other projects

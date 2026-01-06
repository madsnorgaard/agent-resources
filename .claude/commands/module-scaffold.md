---
name: module-scaffold
description: Generate a new Drupal module with best-practice structure
---

# Drupal Module Scaffolding

Create a new Drupal module following best practices.

## Required Information

Ask the user for:
1. Module machine name (e.g., `my_module`)
2. Module human name (e.g., "My Module")
3. Module description
4. What the module should include:
   - Custom block?
   - Custom form?
   - Custom service?
   - Admin configuration page?
   - Custom permissions?

## Generate Structure

Create the following files based on requirements:

### Always Create

1. `my_module.info.yml` - Module definition
2. `my_module.module` - Hook implementations (even if empty)

### Optional (based on requirements)

3. `my_module.services.yml` - If services needed
4. `my_module.routing.yml` - If routes needed
5. `my_module.permissions.yml` - If custom permissions
6. `my_module.links.menu.yml` - If admin menu links
7. `config/install/*.yml` - Default configuration
8. `config/schema/*.schema.yml` - Config schema
9. `src/Controller/` - Controllers
10. `src/Form/` - Forms
11. `src/Plugin/Block/` - Custom blocks
12. `src/Service/` - Services

## Templates

### info.yml
```yaml
name: 'Module Name'
type: module
description: 'Module description.'
core_version_requirement: ^10 || ^11
package: Custom
```

### Basic Service
```yaml
services:
  my_module.example:
    class: Drupal\my_module\Service\ExampleService
    arguments: ['@entity_type.manager']
```

## After Generation

1. Enable with: `drush en my_module`
2. Clear cache: `drush cr`
3. Verify: Check module appears in admin/modules

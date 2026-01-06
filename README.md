# Agent Resources

Personal collection of Claude Code resources for Drupal development and company projects.

## Installation

Install any resource with a single command:

```bash
# Skills
uvx add-skill madsnorgaard/drupal-expert
uvx add-skill madsnorgaard/ddev-expert
uvx add-skill madsnorgaard/docker-local

# Agents
uvx add-agent madsnorgaard/drupal-reviewer

# Commands
uvx add-command madsnorgaard/drush-check
uvx add-command madsnorgaard/module-scaffold
uvx add-command madsnorgaard/config-export
```

## Available Resources

### Skills

| Skill | Description |
|-------|-------------|
| `drupal-expert` | Drupal 10/11 development expertise - modules, themes, services, hooks |
| `ddev-expert` | DDEV local development - commands, configuration, troubleshooting |
| `docker-local` | Custom Docker Compose patterns - expandable template for your setups |

### Agents

| Agent | Description |
|-------|-------------|
| `drupal-reviewer` | Code review for Drupal projects - security, standards, performance |

### Commands

| Command | Description |
|---------|-------------|
| `drush-check` | Run health checks on a Drupal site |
| `module-scaffold` | Generate a new Drupal module with best-practice structure |
| `config-export` | Export Drupal configuration with proper workflow |

## Usage Examples

After installing the drupal-expert skill, Claude will automatically apply Drupal best practices when you work on Drupal code.

After installing the drupal-reviewer agent, you can ask Claude to review your Drupal code and it will use the specialized reviewer.

## Adding More Resources

Add new resources by creating files in:

- `.claude/skills/<skill-name>/SKILL.md` - For skills
- `.claude/agents/<agent-name>.md` - For agents
- `.claude/commands/<command-name>.md` - For slash commands

Push to GitHub and they're immediately available.

## License

MIT

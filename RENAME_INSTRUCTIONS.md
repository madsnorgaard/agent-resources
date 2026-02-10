# GitHub Repository Rename Instructions

This PR updates all documentation to reflect the new repository name **"drupal-agent-resources"**. To complete the rename process, follow these steps:

## Step 1: Rename the Repository on GitHub

1. Go to **Settings** in your GitHub repository
2. Scroll down to the **Repository name** section
3. Change the name from `agent-resources` to `drupal-agent-resources`
4. Click **Rename**

GitHub will automatically set up redirects from the old URL to the new one, so all existing links and `agr add madsnorgaard/<resource>` commands will continue to work.

## Step 2: Update Local Git Remote (Optional)

If you have local clones, update the remote URL:

```bash
git remote set-url origin https://github.com/madsnorgaard/drupal-agent-resources.git
```

## Why This Rename Makes Sense

### Current State
- Repository name: `agent-resources` (generic)
- All content: 100% Drupal-focused resources
  - 5 skills (drupal-expert, drupal-security, drupal-migration, ddev-expert, docker-local)
  - 5 commands (drush-check, module-scaffold, config-export, security-audit, performance-check)
  - 1 agent (drupal-reviewer)

### Benefits of Renaming
1. **Better Discoverability**: Drupal developers searching for Claude Code resources will find it more easily
2. **Clear Purpose**: The name accurately reflects the repository's Drupal-specific focus
3. **Differentiation**: Clearly distinguishes this repository from Kasper Junge's generic `agent-resources` CLI tool
4. **Accurate Branding**: Aligns the repository name with its documentation and content

### Backward Compatibility
- GitHub automatically redirects from the old URL to the new one
- All existing `agr add madsnorgaard/<resource>` commands will continue to work
- Users with bookmarks or links will be automatically redirected
- The CLI tool name remains `agent-resources` (Kasper Junge's package manager)

## What Was Updated in This PR

- ✅ README.md title: "Agent Resources" → "Drupal Agent Resources"
- ✅ CLAUDE.md title: Updated to match
- ✅ ROADMAP.md title and references: Updated to "drupal-agent-resources repository"
- ✅ Verified all installation commands reference the correct CLI tool (`agent-resources`)
- ✅ Ensured clear distinction between the repository name and the CLI tool name

## After Renaming

Once the repository is renamed, you can:
1. Delete this RENAME_INSTRUCTIONS.md file
2. Announce the rename to users (with reassurance about redirects)
3. Continue development as normal

All documentation is already updated and ready for the new name!

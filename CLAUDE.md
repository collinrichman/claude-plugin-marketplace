# Claude Plugin Marketplace

Personal Claude Code plugin marketplace owned by Collin Richman.

## Repository Structure

```
.claude-plugin/marketplace.json  - Marketplace manifest (plugin catalog)
plugins/clr-<name>/              - Individual plugins
plugins/clr-<name>/.claude-plugin/plugin.json - Plugin manifest
```

## Key Rules

- ALL names must be prefixed with `clr-` — plugin names, skill names, agent names, hook names, etc.
  - This applies to directory names, frontmatter `name` fields, and `plugin.json` names
  - Examples: `clr-hello-world`, `clr-greeting`, `clr-my-agent`
- Every plugin must have a `.claude-plugin/plugin.json` with `name` and `description`
- Plugin components (skills/, agents/, hooks/) go at the plugin root, NOT inside `.claude-plugin/`
- NEVER create commands — use skills instead (they serve the same purpose)
- Plugin names must be kebab-case (after the `clr-` prefix)
- Versions are managed only in `.claude-plugin/marketplace.json`, NOT in individual plugin.json files
- Use semantic versioning
- Marketplace name is `clr-plugins` — do not use reserved names (claude-*, anthropic-*, etc.)

## Adding a New Plugin

1. Create `plugins/clr-<name>/.claude-plugin/plugin.json` (name must start with `clr-`)
2. Add plugin components at `plugins/clr-<name>/` (skills/, agents/, hooks/) — all component names must also start with `clr-`
3. Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array with a `version`
4. Do NOT put `version` in individual `plugin.json` files — version only in marketplace.json

## Testing Locally

```bash
claude --plugin-dir ./plugins/clr-<name>
```

## Validation

```bash
claude plugin validate .
```

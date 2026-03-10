# Claude Plugin Marketplace

Personal Claude Code plugin marketplace owned by Collin Richman.

## Repository Structure

```
.claude-plugin/marketplace.json  - Marketplace manifest (plugin catalog)
plugins/<name>/                  - Individual plugins
plugins/<name>/.claude-plugin/plugin.json - Plugin manifest
```

## Key Rules

- Every plugin must have a `.claude-plugin/plugin.json` with `name` and `description`
- Plugin components (skills/, agents/, hooks/) go at the plugin root, NOT inside `.claude-plugin/`
- NEVER create commands — use skills instead (they serve the same purpose)
- Plugin names must be kebab-case
- Versions are managed only in `.claude-plugin/marketplace.json`, NOT in individual plugin.json files
- Use semantic versioning
- Marketplace name is `collin-plugins` — do not use reserved names (claude-*, anthropic-*, etc.)

## Adding a New Plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json`
2. Add plugin components at `plugins/<name>/` (skills/, agents/, hooks/)
3. Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array with a `version`
4. Do NOT put `version` in individual `plugin.json` files — version only in marketplace.json

## Testing Locally

```bash
claude --plugin-dir ./plugins/<name>
```

## Validation

```bash
claude plugin validate .
```

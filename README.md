# collin-plugins

Personal Claude Code plugin marketplace.

## Install

### 1. Add the marketplace

```bash
/plugin marketplace add collinrichman/claude-plugin-marketplace
```

### 2. Install a plugin

```bash
/plugin install hello-world@collin-plugins
```

### 3. Enable auto-updates (optional)

1. Run `/plugin`
2. Go to the **Marketplaces** tab
3. Select **collin-plugins**
4. Select **Enable auto-update**

Auto-updates run at Claude Code startup and will notify you when plugins are updated.

## Available Plugins

| Plugin | Description |
|--------|-------------|
| `hello-world` | A simple hello world plugin to verify marketplace setup |

## Contributing

### Adding a new plugin

1. Create `plugins/<your-plugin>/.claude-plugin/plugin.json`:
   ```json
   {
     "name": "your-plugin",
     "description": "What it does",
     "author": { "name": "Your Name" }
   }
   ```
   Note: Do **not** put `version` in plugin.json — versions are managed only in the marketplace manifest.
2. Add components at the plugin root (`skills/`, `agents/`, `hooks/`)
3. Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array
4. Open a PR

### Updating an existing plugin

1. Make your changes in `plugins/<name>/`
2. Bump the `version` in `.claude-plugin/marketplace.json`
3. Open a PR

## Local Testing

Test a plugin without installing it:

```bash
claude --plugin-dir ./plugins/hello-world
```

Validate the marketplace structure:

```bash
claude plugin validate .
```

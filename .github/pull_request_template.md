## Summary

<!-- Brief description of what this PR does -->

## Plugin(s) affected

<!-- List plugins added, modified, or removed -->

## Checklist

- [ ] All names (plugin, skills, agents, hooks) are prefixed with `clr-`
- [ ] Plugin has a valid `.claude-plugin/plugin.json` with name and description (no version)
- [ ] Plugin components are at the plugin root (not inside `.claude-plugin/`)
- [ ] Marketplace entry added/updated in `.claude-plugin/marketplace.json`
- [ ] Version bumped in `.claude-plugin/marketplace.json` (not in individual plugin.json)
- [ ] Tested locally with `claude --plugin-dir ./plugins/clr-<name>`
- [ ] `claude plugin validate .` passes

# Location Detection Algorithm

Determine where a skill or command lives and classify it for the appropriate update strategy.

## Input Parsing

Parse the target name from `$0`:

- **Simple name** (e.g., `my-skill`) — Search all locations in priority order
- **Namespaced name** (e.g., `plugin-name:skill-name`) — Split on `:` to get the plugin name and skill name. Search plugin locations first, using the plugin name to narrow the search.

## Search Order

Search these locations in order. Stop at the first match found:

### 1. Project Skills

```
.claude/skills/<name>/SKILL.md
```

Check the current working directory for a project-level skill. The skill directory name should match the target name.

### 2. Project Commands

```
.claude/commands/<name>.md
```

Check for a legacy command file. Commands are single `.md` files (not directories).

### 3. User Skills

```
~/.claude/skills/<name>/SKILL.md
```

Check the user's personal skills directory.

### 4. User Commands

```
~/.claude/commands/<name>.md
```

Check the user's personal commands directory.

### 5. Plugin Skills (Current Repo)

```
plugins/*/skills/<name>/SKILL.md
```

Search all plugin directories in the current repository. If the target was namespaced (`plugin-name:skill-name`), search only `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`.

Also check for skills in nested plugin structures if the current repo has a non-standard layout.

### 6. Installed Plugin Skills

Search the Claude Code plugin cache for installed plugins:

```
~/.claude/plugins/cache/*/skills/<name>/SKILL.md
```

For namespaced names, narrow to:
```
~/.claude/plugins/cache/<plugin-name>/*/skills/<skill-name>/SKILL.md
```

Use Glob to search these paths. The cache directory structure may include version subdirectories.

## Classification Rules

After finding the target file, classify it into one of these types:

### Project Skill
- **Location:** `.claude/skills/<name>/SKILL.md` in the current repo
- **Action:** Direct edit, no versioning required
- **Files to read:** SKILL.md + any files in references/, scripts/, assets/

### Project Command
- **Location:** `.claude/commands/<name>.md` in the current repo
- **Action:** Direct edit, no versioning required
- **Files to read:** The command `.md` file only

### User Skill
- **Location:** `~/.claude/skills/<name>/SKILL.md`
- **Action:** Direct edit, no versioning required
- **Files to read:** SKILL.md + any files in references/, scripts/, assets/

### User Command
- **Location:** `~/.claude/commands/<name>.md`
- **Action:** Direct edit, no versioning required
- **Files to read:** The command `.md` file only

### Plugin Skill (Local — Current Repo)
- **Location:** `plugins/*/skills/<name>/SKILL.md` in the current repo
- **Indicator:** The file path is inside the current working directory and inside a `plugins/` directory that has a sibling `.claude-plugin/marketplace.json`
- **Action:** Direct edit + bump version in `.claude-plugin/marketplace.json`
- **Files to read:** SKILL.md + supporting files + marketplace.json (to know current version)
- **Version bump:** Find the plugin's entry in marketplace.json `plugins` array, increment appropriately

### Plugin Skill (Remote — Owned)
- **Location:** Found in `~/.claude/plugins/cache/` or another installed plugin location
- **Indicator:** The plugin originates from `collinrichman/claude-plugin-marketplace`
- **Detection:** Check for origin indicators:
  1. Look for a `.git` directory or remote URL in the plugin's cached source
  2. Check if the plugin name matches an entry in a known marketplace (e.g., `clr-plugins`)
  3. Check if the cached plugin path contains `claude-plugin-marketplace` or `clr-plugins`
  4. As a fallback, ask the user to confirm the plugin's origin
- **Action:** Create a GitHub issue in `collinrichman/claude-plugin-marketplace`
- **Files to read:** The cached SKILL.md + supporting files (read-only, for analysis)

### Plugin Skill (Remote — Unowned)
- **Location:** Found in `~/.claude/plugins/cache/` or another installed plugin location
- **Indicator:** The plugin does NOT originate from `collinrichman/claude-plugin-marketplace`
- **Action:** Inform the user that automatic improvement is not supported. Provide analysis findings for manual use.

## Disambiguation

If multiple matches are found across locations:
1. Present all matches to the user with their locations
2. Ask which one to improve
3. If the user specified a namespaced name, prefer the plugin match

If no match is found:
1. Report that the skill or command was not found
2. Suggest checking the name spelling
3. List available skills/commands in the most likely locations using Glob

## Reading the Target

After locating the target, read all relevant files:

1. Read the main file (SKILL.md or command .md)
2. List the skill directory contents to find supporting files
3. Read any references/ files — these may contain instructions that affect behavior
4. Note the directory structure for context (does it have scripts/? assets/?)

For plugin skills in the current repo, also read the marketplace.json entry to know the current version number.

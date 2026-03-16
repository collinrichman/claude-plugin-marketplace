---
name: clr-skill-creator
description: Use when the user asks to "create a plugin", "create a skill", "add a skill", "scaffold a plugin", "add a new plugin to the marketplace", "build a skill", or wants guidance on plugin/skill structure in the clr-plugins marketplace. Also use when the user says "new plugin", "new skill", "skill creator", or describes functionality they want to package as a plugin — even if they don't use the word "plugin" or "skill" explicitly.
argument-hint: "new-plugin|add-skill"
---

# Skill Creator for clr-plugins Marketplace

Guided workflow for creating plugins and skills in the clr-plugins marketplace. Handles both creating new plugins from scratch and adding skills to existing plugins, enforcing marketplace conventions throughout.

## Quick Decision

Determine the user's intent:

- **Path A: Create New Plugin** - No existing plugin fits; create a new one with initial skill
- **Path B: Add Skill to Existing Plugin** - Add a skill to a plugin that already exists in `plugins/`

Ask the user which path if not clear from context.

## Marketplace Conventions

These rules apply to ALL components created:

- ALL names must start with `clr-` (plugin names, skill names, agent names, hook names)
- Plugin names use kebab-case after the prefix (e.g., `clr-pdf-editor`)
- Every plugin needs `.claude-plugin/plugin.json` with `name` and `description`
- Plugin components (skills/, agents/, hooks/) go at the plugin root, NOT inside `.claude-plugin/`
- NEVER create commands — use skills instead
- Versions live ONLY in `.claude-plugin/marketplace.json`, not in individual plugin.json files

## Path A: Create New Plugin

### Step 1: Gather Requirements

Understand the plugin's purpose:
- What problem does it solve?
- What skills should it include initially?
- What would a user say to trigger those skills?

### Step 2: Create Plugin Structure

```
plugins/clr-<name>/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── clr-<skill-name>/
        ├── SKILL.md
        ├── references/    (if needed)
        ├── scripts/       (if needed)
        └── assets/        (if needed)
```

Create the directory structure, then populate:

**plugin.json** (no version field):
```json
{
  "name": "clr-<name>",
  "description": "<what the plugin does>",
  "author": {
    "name": "Collin Richman"
  }
}
```

### Step 3: Develop the Initial Skill

Follow the "Skill Development" section below.

### Step 4: Register in Marketplace

Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array:

```json
{
  "name": "clr-<name>",
  "source": "./plugins/clr-<name>",
  "description": "<description>",
  "version": "1.0.0",
  "author": {
    "name": "Collin Richman"
  },
  "keywords": ["<keyword1>", "<keyword2>"]
}
```

## Path B: Add Skill to Existing Plugin

### Step 1: Identify Target Plugin

List plugins in `plugins/` and confirm the target with the user.

### Step 2: Create Skill Directory

```bash
mkdir -p plugins/clr-<plugin>/skills/clr-<skill-name>
```

Create only the subdirectories actually needed (references/, scripts/, assets/).

### Step 3: Develop the Skill

Follow the "Skill Development" section below.

## Skill Development

### Understanding the Skill

Before writing, gather concrete examples:
- What tasks will this skill handle?
- What would a user say to trigger it?
- What scripts, references, or assets would help?

Ground skill content in real expertise, not general LLM knowledge. Effective approaches:
- **Extract from a hands-on task** — Complete the task in conversation first, noting corrections and context provided along the way, then distill the reusable pattern into a skill.
- **Synthesize from project artifacts** — Feed internal docs, runbooks, API specs, code review comments, or incident reports into the creation process. Project-specific material produces far better skills than generic best-practices articles.
- **Add what the agent lacks, omit what it knows** — Focus on project-specific conventions, non-obvious edge cases, and particular tools/APIs. Do not explain general concepts the agent already understands.

Analyze each example to identify reusable resources:
- **Repeated code** → `scripts/` (deterministic, token-efficient)
- **Domain knowledge** → `references/` (loaded as needed)
- **Output templates** → `assets/` (used without loading into context)

### Writing SKILL.md

#### Frontmatter

```yaml
---
name: clr-<skill-name>
description: Use when the user asks to "<phrase 1>", "<phrase 2>", "<phrase 3>", or <broader trigger condition>. <Brief summary of what it provides>.
---
```

Description rules:
- Use imperative phrasing ("Use when..." not "This skill should be used when...")
- Include 3-5 specific trigger phrases in quotes
- Focus on user intent, not implementation — describe what the user is trying to achieve, not the skill's internal mechanics
- Be concrete about scenarios
- Cover implicit triggers — users who describe what they want without using exact keywords

### Additional Frontmatter Fields

Beyond `name`, `description`, and `argument-hint`, these fields control invocation behavior:

- **`disable-model-invocation: true`** — Prevents Claude from auto-triggering. Only manual `/skill-name` invocation works. Use for skills with side effects (deploy, commit, destructive operations).
- **`user-invocable: false`** — Hides from user's slash command list. Use for background knowledge skills that only Claude should invoke.
- **`allowed-tools`** — Pre-approves specific tools so they run without permission prompts. Space-delimited. Supports patterns: `Bash(git:*) Bash(jq:*) Read`. Use for reducing permission friction or restricting to read-only operations.
- **`context: fork`** — Runs the skill in an isolated subagent context. Use when the skill is heavy, self-contained, or should not pollute the main conversation context.
- **`agent`** — Which subagent type to use when `context: fork` is set. Options: `Explore`, `Plan`, `general-purpose`, or a custom agent name from `.claude/agents/`. Default: `general-purpose`.
- **`model`** — Model override for when this skill is active.
- **`hooks`** — Hooks scoped to this skill's lifecycle. See Claude Code hooks documentation for the configuration format.

### String Substitutions

Skills support these substitution patterns in SKILL.md content:

- **`$ARGUMENTS`** — Full argument string passed when skill is invoked (e.g., `/clr-my-skill foo bar` → `$ARGUMENTS` = `"foo bar"`)
- **`$ARGUMENTS[N]` / `$N`** — Positional argument access (`$1` = first arg, `$2` = second)
- **`${CLAUDE_SKILL_DIR}`** — Absolute path to the skill's directory. Use to reference bundled files: `Read ${CLAUDE_SKILL_DIR}/references/patterns.md`
- **`${CLAUDE_SESSION_ID}`** — Current session ID. Use for session-specific tracking or logging.

### Dynamic Context Injection

Prefix a backtick-wrapped shell command with `!` to inject its stdout into the skill content at load time. For example, writing an exclamation mark followed by a command in backticks (like date or git status) will execute that command and inline the output when the skill loads.

Use sparingly — only when the skill needs live data that changes between invocations.

#### Body

Write the body in **imperative/infinitive form** (verb-first instructions). Target 1,500-2,000 words.

**Correct:** "Parse the configuration file. Validate inputs before processing."
**Incorrect:** "You should parse the configuration file. You need to validate inputs."

#### Structure the Body

1. **Overview** - 1-2 sentences on what the skill enables
2. **Workflow** - Step-by-step process or decision tree
3. **Key concepts** - Essential knowledge for the domain
4. **Resource references** - Point to bundled references/, scripts/, assets/

Keep SKILL.md lean. Move detailed content to `references/`:
- Detailed patterns → `references/patterns.md`
- Advanced techniques → `references/advanced.md`
- API docs or schemas → `references/api-reference.md`

### Adding Resources

Create only what the skill actually needs:

**scripts/** - For tasks requiring deterministic reliability or repeated code. Make scripts executable.

**references/** - For documentation Claude should consult while working. Keep information in ONE place (SKILL.md or references, not both).

**assets/** - For templates, boilerplate, or files used in output.

## Validation

After creating the skill, verify:

1. **Structure**: SKILL.md exists in `plugins/clr-<plugin>/skills/clr-<skill-name>/`
2. **Frontmatter**: Has `name` (with clr- prefix) and `description`
3. **Description**: Imperative phrasing ("Use when..."), 3-5 specific trigger phrases
4. **Writing style**: Imperative form, no second person
5. **Progressive disclosure**: SKILL.md lean (~1,500-2,000 words), details in references/
6. **References**: All referenced files exist
7. **Convention compliance**: All names start with `clr-`, no commands created

For detailed anatomy of a well-structured skill, consult **`references/skill-anatomy.md`**.

## Testing

Test the plugin locally:

```bash
claude --plugin-dir ./plugins/clr-<name>
```

Invoke the skill and verify it triggers on expected phrases.

### Eval-Driven Testing

For more rigorous testing, design trigger evals:

1. **Should-trigger queries** — Phrases that must activate the skill (e.g., "create a plugin", "add a skill to my project")
2. **Should-NOT-trigger queries** — Phrases that are similar but should not activate (e.g., "delete a plugin", "list all skills")
3. **Implicit trigger queries** — Phrases that don't use keywords but describe the intent (e.g., "I want to package this as something reusable")

Test each query and verify the skill triggers (or doesn't) correctly. Track pass/fail rates and iterate on the description to improve accuracy.

## Iteration

After testing, common improvements include:
- Strengthening trigger phrases in the description based on eval results
- Adding implicit trigger coverage for missed intent patterns
- Moving long sections from SKILL.md to references/
- Adding missing scripts or examples
- Clarifying ambiguous instructions

## Additional Resources

### Reference Files

- **`references/skill-anatomy.md`** - Detailed skill structure guide, frontmatter fields, progressive disclosure patterns, writing style requirements, and validation checklist

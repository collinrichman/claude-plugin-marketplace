---
name: clr-skill-creator
description: This skill should be used when the user asks to "create a plugin", "create a skill", "add a skill", "scaffold a plugin", "add a new plugin to the marketplace", "build a skill", or wants guidance on plugin/skill structure in the clr-plugins marketplace. Also triggers on "new plugin", "new skill", or "skill creator".
argument-hint: [new-plugin|add-skill]
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
  "source": "clr-<name>",
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

Analyze each example to identify reusable resources:
- **Repeated code** → `scripts/` (deterministic, token-efficient)
- **Domain knowledge** → `references/` (loaded as needed)
- **Output templates** → `assets/` (used without loading into context)

### Writing SKILL.md

#### Frontmatter

```yaml
---
name: clr-<skill-name>
description: This skill should be used when the user asks to "<phrase 1>", "<phrase 2>", "<phrase 3>", or <broader trigger condition>. <Brief summary of what it provides>.
---
```

Description rules:
- Use third person ("This skill should be used when...")
- Include 3-5 specific trigger phrases in quotes
- Be concrete about scenarios

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
3. **Description**: Third person, 3-5 specific trigger phrases
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

## Iteration

After testing, common improvements include:
- Strengthening trigger phrases in the description
- Moving long sections from SKILL.md to references/
- Adding missing scripts or examples
- Clarifying ambiguous instructions

## Additional Resources

### Reference Files

- **`references/skill-anatomy.md`** - Detailed skill structure guide, frontmatter fields, progressive disclosure patterns, writing style requirements, and validation checklist

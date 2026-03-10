# Skill Anatomy Reference

## SKILL.md Structure

Every skill consists of a required SKILL.md file and optional bundled resources:

```
clr-skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter metadata (required)
│   │   ├── name: (required, must start with clr-)
│   │   └── description: (required)
│   └── Markdown instructions (required)
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Python/Bash/etc.)
    ├── references/       - Documentation loaded into context as needed
    └── assets/           - Files used in output (templates, icons, etc.)
```

## Frontmatter Fields

### Required Fields

- **name**: Skill identifier. Must start with `clr-`. Use kebab-case (e.g., `clr-pdf-editor`).
- **description**: When this skill triggers. Use imperative phrasing with specific trigger phrases.

### Optional Fields

- **argument-hint**: Hint text shown to user for expected arguments (e.g., `[new-plugin|add-skill]`).
- **disable-model-invocation**: Set to `true` to prevent Claude from auto-triggering. Only manual `/skill-name` invocation works. Use for skills with side effects (deploy, commit).
- **user-invocable**: Set to `false` to hide from the user's slash command list. Use for background knowledge skills that only Claude should invoke.
- **allowed-tools**: Restricts which tools the skill can use. Use for security-sensitive skills (e.g., read-only research skills).
- **context**: Set to `fork` to run in an isolated subagent. Use for heavy, self-contained skills.

## Description Best Practices

### Imperative Phrasing with Trigger Phrases

The description determines when Claude loads the skill. Use imperative phrasing ("Use when...") and include specific phrases users would say. Focus on user intent, not implementation mechanics.

**Good examples:**

```yaml
description: Use when the user asks to "create a hook", "add a PreToolUse hook", "validate tool use", or mentions hook events (PreToolUse, PostToolUse, Stop).
```

```yaml
description: Use when the user asks to "edit a PDF", "rotate pages", "merge PDFs", "extract text from PDF", or needs PDF manipulation guidance.
```

**Bad examples:**

```yaml
description: Provides guidance for working with hooks.  # Vague, no triggers
description: This skill should be used when editing PDFs.  # Passive, not imperative
description: Load when user needs help.                  # Too generic
```

## Progressive Disclosure

Skills use a three-level loading system:

1. **Metadata (name + description)** - Always in context (~100 words)
2. **SKILL.md body** - Loaded when skill triggers (<5k words, ideally 1,500-2,000)
3. **Bundled resources** - Loaded as needed by Claude (unlimited)

### What Goes Where

**SKILL.md (always loaded when triggered):**
- Core concepts and overview
- Essential procedures and workflows
- Quick reference tables
- Pointers to references/examples/scripts

**references/ (loaded as needed):**
- Detailed patterns and advanced techniques
- Comprehensive documentation
- Edge cases and troubleshooting

**scripts/ (executed, not always read):**
- Validation utilities
- Automation helpers
- Parsing tools

**assets/ (used in output, not loaded into context):**
- Templates
- Boilerplate code
- Images/icons

## Writing Style Requirements

### Imperative/Infinitive Form

Write using verb-first instructions, not second person:

**Correct:**
```markdown
To create a hook, define the event type.
Configure the MCP server with authentication.
Validate settings before use.
```

**Incorrect:**
```markdown
You should start by creating a hook.
You need to configure the MCP server.
You can validate settings before use.
```

### Objective, Instructional Language

Focus on what to do, not who should do it:

**Correct:**
```markdown
Parse the frontmatter using sed.
Extract fields with grep.
```

**Incorrect:**
```markdown
You can parse the frontmatter...
Claude should extract fields...
```

## Common Mistakes

### 1. Weak Trigger Description

Problem: Vague descriptions that don't trigger reliably.

Fix: Include 3-5 specific phrases users would say, in quotes.

### 2. Too Much in SKILL.md

Problem: SKILL.md over 3,000 words bloats context when loaded.

Fix: Move detailed content to `references/`. Keep SKILL.md to 1,500-2,000 words.

### 3. Missing Resource References

Problem: Claude doesn't know supporting files exist.

Fix: Reference all `references/`, `scripts/`, and `assets/` files explicitly in SKILL.md.

### 4. Forgetting clr- Prefix

Problem: Names without the `clr-` prefix violate marketplace conventions.

Fix: All names (plugin, skill, agent, hook) must start with `clr-`.

## Validation Checklist

**Structure:**
- [ ] SKILL.md exists with valid YAML frontmatter
- [ ] Frontmatter has `name` (with clr- prefix) and `description`
- [ ] Markdown body is present and substantial
- [ ] Referenced files actually exist

**Description Quality:**
- [ ] Uses imperative phrasing ("Use when...")
- [ ] Includes 3-5 specific trigger phrases
- [ ] Focuses on user intent, not implementation
- [ ] Lists concrete scenarios

**Content Quality:**
- [ ] Body uses imperative/infinitive form
- [ ] Body is 1,500-2,000 words (max 5k)
- [ ] Detailed content moved to references/
- [ ] No second person ("you should...")

**Progressive Disclosure:**
- [ ] Core concepts in SKILL.md
- [ ] Detailed docs in references/
- [ ] Working code in scripts/ (if applicable)
- [ ] SKILL.md references these resources

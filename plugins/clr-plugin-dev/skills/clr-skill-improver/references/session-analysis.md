# Session Analysis Checklist

Systematic analysis of how a skill or command performed in the current session. Review the conversation history and apply each dimension below to identify concrete, evidence-backed improvements.

## Before Starting

Scan the full conversation for:
- When and how the target skill was invoked (manual `/name` or auto-triggered)
- What the user was trying to accomplish
- What Claude actually did after the skill loaded
- Where the outcome diverged from the user's intent
- Any explicit user complaints or corrections during the session

## Dimension 1: Trigger Accuracy

Evaluate whether the skill activated at the right time and for the right reasons.

### Check for False Triggers
- Did the skill activate when it should NOT have?
- Was the description too broad, matching unrelated user intent?
- Did keyword overlap with another skill cause a wrong match?

### Check for Missed Triggers
- Did the user describe something the skill should handle, but it did not activate?
- Were there phrases or intents the description does not cover?
- Did the user have to manually invoke `/name` when auto-trigger should have worked?

### Check Description Effectiveness
- Does the description use imperative phrasing ("Use when...")?
- Are the trigger phrases specific enough to avoid false positives?
- Are there implicit intent patterns missing (users who describe what they want without using keywords)?
- Does the description cover 3-5 concrete trigger phrases?

### Evidence to Collect
- The exact user message that triggered (or should have triggered) the skill
- Whether the skill loaded automatically or was manually invoked
- Any competing skills that also triggered or should have triggered instead

## Dimension 2: Instruction Quality

Evaluate whether the skill's instructions guided Claude to the right outcome.

### Check Instruction Clarity
- Were any steps ambiguous enough that Claude interpreted them incorrectly?
- Were instructions missing for edge cases that occurred during the session?
- Did Claude have to improvise or deviate from the instructions?

### Check Step Ordering
- Did the workflow steps proceed in a logical order?
- Were there steps that should have come earlier (e.g., validation before action)?
- Did Claude skip steps or execute them out of order?

### Check Completeness
- Were there gaps in the instructions that left Claude guessing?
- Did the user have to provide additional guidance that should have been in the skill?
- Are there common variations the skill does not address?

### Check Accuracy
- Did the instructions contain incorrect information (wrong paths, outdated patterns)?
- Did the instructions reference tools or features that do not work as described?
- Were code examples or templates correct and up-to-date?

### Evidence to Collect
- Specific instruction text that Claude followed incorrectly or had to interpret
- Steps where Claude deviated from the skill's guidance
- User corrections that indicate missing or wrong instructions
- Points where Claude asked for clarification that the skill should have covered

## Dimension 3: Efficiency and Permissions

Evaluate whether the skill used tools and permissions effectively.

### Check Tool Usage
- Were there unnecessary tool calls (reading files that were not needed, redundant searches)?
- Did Claude use the right tool for each task (e.g., Grep vs Read, Edit vs Write)?
- Were there sequences of tool calls that could be parallelized?
- Did Claude repeat the same search or read multiple times?

### Check Permission Friction
- Were there excessive permission prompts that slowed the workflow?
- Could `allowed-tools` in the frontmatter reduce prompts for expected operations?
- Is `allowed-tools` too permissive, granting access to tools the skill does not need?
- Would `context: fork` reduce permission noise by isolating the skill?

### Check Context Efficiency
- Did the skill load more content into context than needed?
- Should verbose content move from SKILL.md to references/ for on-demand loading?
- Were reference files loaded when they were not needed?
- Is the skill body too long (over 2,000 words) for what it accomplishes?

### Evidence to Collect
- Tool calls that were redundant or unnecessary
- Permission prompts that interrupted the workflow
- Times Claude loaded content it did not use
- Total number of tool calls vs. what a streamlined workflow would require

## Dimension 4: Output Quality

Evaluate the gap between what the user expected and what the skill produced.

### Check Result Accuracy
- Did the skill produce the correct result?
- Were there errors, omissions, or incorrect content in the output?
- Did the output match the user's stated requirements?

### Check Result Completeness
- Was anything missing from the output that the user expected?
- Did the skill handle all aspects of the request, or did it miss some?
- Were there follow-up corrections the user had to make?

### Check Result Format
- Was the output in the expected format (code, text, structured data)?
- Did the output follow project conventions and style?
- Was the output easy for the user to use without modification?

### Evidence to Collect
- The final output or result the skill produced
- User reactions (approval, corrections, complaints)
- Differences between expected and actual output
- Follow-up work the user had to do after the skill finished

## Dimension 5: Supporting Files

Evaluate whether the skill's file structure supports its behavior effectively.

### Check SKILL.md Size
- Is the main SKILL.md within the recommended range (1,500-2,000 words)?
- Could any sections move to references/ without losing essential context?
- Is the SKILL.md too short, missing important guidance?

### Check Reference Usage
- Are references/ files actually loaded when needed?
- Does SKILL.md clearly describe when to load each reference?
- Are there references that should exist but do not?
- Are there references that exist but are never used?

### Check for Missing Resources
- Would a script in scripts/ improve reliability for deterministic tasks?
- Would a template in assets/ ensure consistent output format?
- Would an example file help Claude understand expected behavior?

### Evidence to Collect
- Which reference files were loaded during the session
- Which reference files were NOT loaded but should have been
- Points where a script or template would have produced better results

## Synthesizing Improvements

After completing all five dimensions:

1. **Rank findings by impact** — Which issues had the most negative effect on the user's experience?
2. **Filter for evidence** — Discard findings that are speculative. Keep only those supported by concrete session evidence.
3. **Group related findings** — Multiple symptoms may point to a single root cause.
4. **Propose specific edits** — For each finding, identify the exact file and content to change. Include old content and new content.
5. **Assess scope** — Are the changes a patch-level fix (typo, minor rewording) or a minor-version change (new functionality, restructuring)?

Present findings in order of impact, with the most critical improvements first.

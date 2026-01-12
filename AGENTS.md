# AGENTS.md

This file provides guidance to coding agents when working with code in this repository.

## Repository Purpose

This is a collection of Claude Code skills for Sprite environments. Skills are custom slash commands that provide step-by-step instructions for common tasks.

Reference: https://code.claude.com/docs/en/skills

## Skill Structure

Each skill is a directory containing a `SKILL.md` file:

```
skill-name/
  SKILL.md
  scripts/       # Optional: executable scripts
  references/    # Optional: documentation loaded into context
  assets/        # Optional: templates and binary files
```

## YAML Frontmatter Requirements

The frontmatter must start on line 1 (no blank lines before `---`):

```yaml
---
name: skill-name
description: Describes what the skill does and when to use it
---
```

**name** (required):
- Max 64 characters
- Lowercase letters, numbers, and hyphens only
- Cannot contain "anthropic" or "claude"
- Prefer gerund form: `processing-pdfs`, `analyzing-spreadsheets`

**description** (required):
- Max 1024 characters
- Write in third person ("Processes Excel files..." not "I can help you...")
- Include both what it does AND when to use it
- Be specific with key terms for discovery

## Creating New Skills

1. Create a directory with a descriptive kebab-case name
2. Add `SKILL.md` with frontmatter and markdown instructions
3. Keep SKILL.md body under 500 lines; split larger content into reference files
4. Use progressive disclosure: SKILL.md points to detailed files as needed
5. For complex skills, add `scripts/` for executable code and `references/` for documentation

## Current Skills

- **create-phoenix-project**: Creates an Elixir/Phoenix project with LiveView and SQLite in Sprite
- **setup-tidewave-mcp**: Configures Tidewave MCP integration for Phoenix apps

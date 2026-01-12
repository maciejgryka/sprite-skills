---
name: setup-sprite-phoenix
description: Complete setup for a Phoenix project in Sprite with Tidewave MCP integration for AI-assisted development.
---

# Complete Phoenix Setup in Sprite

## Overview

This skill guides you through setting up a fully-featured Phoenix development environment in Sprite, including Tidewave MCP for AI-assisted development capabilities.

## Skills to Execute

Complete these skills in order:

### 1. Create Phoenix Project

First, set up the Phoenix application with LiveView and SQLite.

**Skill:** `create-phoenix-project`

This will:
- Install mise (if needed)
- Install Erlang and Elixir via mise
- Create a Phoenix project with LiveView and SQLite
- Configure the startup script and Caddy reverse proxy
- Set up Sprite services
- Guide the user through making the URL public

**Wait for the user to confirm the Phoenix app is accessible before proceeding.**

### 2. Set Up Tidewave MCP

Once Phoenix is running and publicly accessible, add Tidewave MCP integration.

**Skill:** `setup-tidewave-mcp`

This will:
- Add Tidewave dependency to the project
- Configure the Tidewave plug in the endpoint
- Restart Phoenix to apply changes
- Verify the MCP endpoint is working

## Completion Checklist

Before declaring this skill complete, verify:

- [ ] Phoenix server is running (`sprite-env services list` shows phoenix active)
- [ ] Caddy proxy is running (`sprite-env services list` shows caddy active)
- [ ] Public URL is accessible (`curl -I https://<sprite-name>.sprites.app/` returns 200)
- [ ] Tidewave MCP endpoint responds (`curl -X POST http://localhost:4000/tidewave/mcp ...`)

## Result

After completion, you'll have:
- A Phoenix app with LiveView running in Sprite
- SQLite database configured
- Public HTTPS URL via Sprite proxy
- Tidewave MCP integration enabling Claude Code to:
  - Evaluate Elixir code in the running app
  - Query the database
  - Access logs
  - Look up documentation

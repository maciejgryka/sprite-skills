---
name: setup-tidewave-mcp
description: Set up Tidewave MCP integration so Claude Code can evaluate Elixir code, query the database, access logs, and get documentation in a running Phoenix app.
---

# Setting Up Tidewave MCP for Claude Code

## Overview

Tidewave MCP gives Claude Code direct access to your running Phoenix app, enabling live code evaluation, database queries, log access, and documentation lookup. Always refer to https://hexdocs.pm/tidewave/mcp.html and https://hexdocs.pm/tidewave/mcp_claude_code.html for up-to-date information about it.

## Available Tools After Setup

- **project_eval** - Execute Elixir code in the running app context
- **get_logs** - Retrieve Phoenix application logs
- **execute_sql_query** - Run SQL queries against the database
- **get_docs** - Get documentation for modules and functions
- **get_source_location** - Find where code is defined
- **get_ecto_schemas** - List all Ecto schemas in the project
- **search_package_docs** - Search Hex documentation

## Step 1: Add Tidewave Dependency

Add to `mix.exs`:

```elixir
defp deps do
  [
    {:tidewave, "~> 0.5", only: :dev},
    # ... other deps
  ]
end
```

Then install:

```bash
mix deps.get
```

## Step 2: Configure Tidewave Plug

Edit `lib/my_app_web/endpoint.ex` and add the Tidewave plug near the top:

```elixir
defmodule MyAppWeb.Endpoint do
  use Phoenix.Endpoint, otp_app: :my_app

  # Tidewave for AI-assisted development
  if Code.ensure_loaded?(Tidewave) do
    plug Tidewave, allow_remote_access: true
  end

  # ... rest of endpoint config
end
```

## Step 3: Restart Phoenix

```bash
sprite-env services signal phoenix TERM
```

## Step 4: Verify MCP Endpoint

Test that Tidewave MCP is responding:

```bash
curl -s -X POST http://localhost:4000/tidewave/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'
```

You should see a JSON response listing available tools.

## Using the MCP Tools

### Evaluate Elixir Code

```bash
curl -s -X POST http://localhost:4000/tidewave/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"project_eval","arguments":{"code":"Enum.map(1..5, & &1 * 2)"}}}'
```

### Get Recent Logs

```bash
curl -s -X POST http://localhost:4000/tidewave/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_logs","arguments":{"tail":10}}}'
```

### Query Database

```bash
curl -s -X POST http://localhost:4000/tidewave/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"execute_sql_query","arguments":{"query":"SELECT name FROM sqlite_master WHERE type=\"table\""}}}'
```

### Get Documentation

```bash
curl -s -X POST http://localhost:4000/tidewave/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_docs","arguments":{"reference":"Enum.map/2"}}}'
```

## What Claude Code Can Do With Tidewave

Once configured, Claude Code can:

1. **Run code in your app's context** - Test functions, debug issues, inspect state
2. **Query your database** - Check data, verify migrations, debug queries
3. **Access logs** - See what's happening in real-time
4. **Look up documentation** - Get help on Elixir/Phoenix functions
5. **Find source locations** - Navigate to module/function definitions

## Troubleshooting

### MCP endpoint returns 404
- Ensure Tidewave is in deps: `mix deps.get && mix compile`
- Ensure the plug is added to endpoint.ex
- Restart Phoenix

### Protocol version error
- Tidewave requires MCP protocol version `2025-03-26` or later

### Verify Tidewave is loaded
```bash
curl http://localhost:4000/tidewave/config
```

Should return JSON with `project_name` and `tidewave_version`.

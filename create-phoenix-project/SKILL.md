---
name: create-phoenix-project
description: Create an Elixir/Phoenix project with LiveView and SQLite in Sprite, using mise for runtime management, and expose it publicly via the Sprite URL.
---

# Creating a Phoenix Project in Sprite

## Overview

This skill covers creating an Elixir/Phoenix project with LiveView and SQLite, then exposing it publicly through the Sprite proxy.

## Step 1: Install Erlang and Elixir with mise

```bash
# Install Erlang/OTP
mise use -g erlang@28

# Install Elixir (will use the installed Erlang)
mise use -g elixir@1.19

# Verify installation
elixir --version
```

## Step 2: Create Phoenix Project

Use Igniter to create a new Phoenix project with LiveView and SQLite:

```bash
mix archive.install hex igniter_new --force
mix igniter.new my_app --with phx.new --with-args="--database sqlite3"
cd my_app
```

This creates a Phoenix project with:
- LiveView enabled by default
- SQLite3 as the database (via `ecto_sqlite3`)
- Standard Phoenix structure

## Step 3: Create a Startup Script

Create `start.sh` to ensure proper environment:

```bash
#!/bin/bash
export PATH="/home/sprite/.local/share/mise/installs/elixir/1.19.5-otp-28/bin:/home/sprite/.local/share/mise/installs/erlang/28.3/bin:$PATH"                                              export HOME=/home/sprite
export MIX_HOME=/home/sprite/.mix
export PORT=4000
cd /home/sprite/my_app
exec mix phx.server
```

Make it executable: `chmod +x start.sh`

## Step 4: Configure for Port 4000

Edit `config/dev.exs` to bind to all interfaces:

```elixir
config :my_app, MyAppWeb.Endpoint,
  http: [ip: {0, 0, 0, 0}, port: 4000],
  check_origin: false,
  # ... rest of config
```

Note: We use port 4000 internally because port 8080 is used by the Sprite proxy.

## Step 5: Create Sprite Service

```bash
sprite-env services create phoenix \
  --cmd /home/sprite/my_app/start.sh \
  --no-stream
```

## Step 6: Set Up Caddy Reverse Proxy

Create `/home/sprite/Caddyfile`:

```
:8080 {
    @assets path /assets/*
    handle @assets {
        reverse_proxy localhost:4000
    }

    @phoenix_live path /live/* /phoenix/*
    handle @phoenix_live {
        reverse_proxy localhost:4000
    }

    handle {
        reverse_proxy localhost:4000
    }
}
```

Create the Caddy service:

```bash
sprite-env services create caddy \
  --cmd /usr/bin/caddy \
  --args "run,--config,/home/sprite/Caddyfile" \
  --http-port 8080 \
  --no-stream
```

## Step 7: Access Your App

Your Phoenix app is now publicly accessible at:
```
https://<your-sprite-name>.sprites.app/
```

## Useful Commands

```bash
# Check services
sprite-env services list

# View Phoenix logs
tail -f /.sprite/logs/services/phoenix.log

# Restart a service (auto-restarts after TERM)
sprite-env services signal phoenix TERM

# Run mix commands
cd /home/sprite/my_app && mix ecto.migrate
```

## Key Points

- Sprite proxy handles HTTPS termination on port 8080
- Phoenix runs on port 4000 internally
- Caddy routes requests from 8080 to 4000
- LiveView websockets work automatically through Caddy
- Hot code reloading works in development
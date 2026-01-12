---
name: phx-gen-auth
description: Add authentication to a Phoenix project using mix phx.gen.auth, generating a complete production-ready auth system with registration, login, and password reset.
---

# Setting Up Phoenix Authentication

## Overview

This skill covers adding authentication to a Phoenix project using `mix phx.gen.auth`, which generates a complete, production-ready authentication system.

**Official Documentation:** https://hexdocs.pm/phoenix/mix_phx_gen_auth.html

## Prerequisites

- Phoenix 1.7+ project
- Ecto configured with a database (PostgreSQL, SQLite, MySQL)

## Step 1: Generate Authentication

Run the generator with your desired context, schema, and table name:

```bash
mix phx.gen.auth Accounts User users
```

**Arguments:**
- `Accounts` - Context module name (groups related functionality)
- `User` - Schema module name
- `users` - Database table name

When prompted, choose between:
- **LiveView-based** (recommended) - Better UX with real-time updates
- **Controller-only** - Traditional request/response flow

## Step 2: Install Dependencies and Migrate

```bash
mix deps.get
mix ecto.migrate
```

## Step 3: Restart Phoenix

If running as a service:
```bash
sprite-env services signal phoenix TERM
# Or recreate
sprite-env services delete phoenix && sprite-env services create phoenix --cmd /path/to/start.sh --no-stream
```

Or manually:
```bash
mix phx.server
```

## Generated Components

### Schemas
| Schema | File | Purpose |
|--------|------|---------|
| `User` | `lib/my_app/accounts/user.ex` | User account data |
| `UserToken` | `lib/my_app/accounts/user_token.ex` | Session and confirmation tokens |

### Database Tables
- `users` - Stores email, hashed_password, confirmed_at
- `users_tokens` - Stores authentication tokens

### Routes Added
| Path | Description |
|------|-------------|
| `/users/register` | New account registration |
| `/users/log_in` | Sign in page |
| `/users/log_out` | Sign out |
| `/users/settings` | Account settings |
| `/users/confirm` | Email confirmation |
| `/dev/mailbox` | View sent emails (dev only) |

### Key Modules
- `MyApp.Accounts` - Context with auth functions
- `MyAppWeb.UserAuth` - Authentication plugs

## Command Options

```bash
mix phx.gen.auth Accounts User users [options]
```

| Option | Description |
|--------|-------------|
| `--live` / `--no-live` | Use LiveView (default) or controllers only |
| `--hashing-lib STRING` | Password hashing: `bcrypt` (default), `pbkdf2`, `argon2` |
| `--binary-id` | Use binary IDs (UUIDs) instead of integers |
| `--table STRING` | Custom database table name |
| `--web STRING` | Custom web module namespace |
| `--merge-with-existing-context` | Add to existing context module |

## Features Included

- **Email/password registration** with validation
- **Email confirmation** via magic link
- **Session management** with secure tokens
- **"Remember me"** functionality
- **Password reset** flow
- **Sudo mode** for sensitive operations

## Authentication Plugs

Use these in your router to protect routes:

```elixir
# In router.ex
import MyAppWeb.UserAuth

# Require authentication
pipe_through [:browser, :require_authenticated_user]

# Optional authentication (sets @current_user if logged in)
pipe_through [:browser, :fetch_current_user]
```

## Accessing Current User

In LiveView:
```elixir
def mount(_params, _session, socket) do
  current_user = socket.assigns.current_scope.user
  {:ok, assign(socket, user: current_user)}
end
```

In controllers:
```elixir
def show(conn, _params) do
  current_user = conn.assigns.current_scope.user
  render(conn, :show, user: current_user)
end
```

## Testing

Run the generated tests:
```bash
mix test test/my_app/accounts_test.exs
mix test test/my_app_web/user_auth_test.exs
```

## Security Notes

1. **Generated code is not auto-updated** - Monitor Phoenix's CHANGELOG.md for security fixes
2. **Password hashing** - bcrypt is secure for most use cases; argon2 for maximum security
3. **Email confirmation** - Enabled by default; users must confirm email to complete registration

## References

- [mix phx.gen.auth documentation](https://hexdocs.pm/phoenix/mix_phx_gen_auth.html)
- [Phoenix Authentication Guide](https://hexdocs.pm/phoenix/authentication.html)
- [Phoenix Security Guide](https://hexdocs.pm/phoenix/security.html)

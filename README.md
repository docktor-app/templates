# docktor-templates

Template repository for [Docktor](https://github.com/docktor-app/docktor). This is
the default source Docktor uses to let users create a new stack from a
ready-made template instead of starting from a blank compose file.

This repo contains no application logic — it's just a plain git repository
with a fixed folder layout that Docktor's template reader knows how to parse.
Any git host works; Docktor clones it (and any other template repository
users add) over `https://`, `git://`, or `ssh://`.

## Layout

```
templates/
  <template>/
    template.yml        # shared metadata for the template
    icon.svg             # optional icon, referenced by template.yml
    <variant>/
      variant.yml          # this variant's metadata
      docker-compose.yml    # this variant's compose file
      .env.example            # optional default environment values
    <other-variant>/
      ...
```

- Each directory directly under `templates/` is one **template** (an
  application, e.g. `nextcloud`).
- Each directory inside a template is one **variant** — a distinct,
  self-contained way to deploy that application (e.g. `default`,
  `with-redis`). A template with only one way to deploy it still needs a
  single variant directory, conventionally named `default`.
- Template and variant directory names must match `^[a-z0-9][a-z0-9-]{0,62}$`.

### `template.yml`

```yaml
schemaVersion: 1
name: Nextcloud
description: A self-hosted file sync and collaboration platform
category: Productivity
icon: icon.svg   # optional, .svg or .png, in the same directory
```

### `variant.yml`

```yaml
name: With Redis
description: Adds a Redis container for file-locking and caching
usage: |
  Optional free-text instructions shown to the user after they create
  a stack from this variant (first-login steps, default credentials, etc.).
```

### `docker-compose.yml`

A normal compose file with a non-empty top-level `services:` key.

### `.env.example` (optional)

Default `KEY=value` environment values. If present, these seed the new
stack's `.env` when a user creates a stack from this variant — the user can
edit every value before deploying.

## Conventions

- Bind mounts under `./volumes/` (named Docker volumes aren't supported).
- Secrets and anything environment-specific go in `.env.example` and are
  referenced from `docker-compose.yml` with `${VAR}` — never hardcoded.
- Any service reading from `.env.example` needs `env_file: .env`, or the
  values a user edits after creating the stack never reach the container.
- Avoid `privileged: true` and mounting the Docker socket unless genuinely
  required — both trigger Docktor's dangerous-config warnings on every stack
  created from the template.

A template or variant that fails validation is excluded, not fatal — see
Docktor's [`docs/templates.md`](https://github.com/docktor-app/docktor/blob/main/docs/templates.md)
for the full format reference, size limits, and rejection rules.

## Templates in this repository

| Template | Variants |
|---|---|
| [`nextcloud`](templates/nextcloud) | `default`, `with-redis`, `behind-proxy` |
| [`immich`](templates/immich) | `default`, `with-external-postgres` |

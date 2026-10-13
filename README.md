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

| Category | Template | Variants |
|---|---|---|
| Productivity | [`memos`](templates/memos) | `default` |
| Productivity | [`nextcloud`](templates/nextcloud) | `default`, `behind-proxy`, `mariadb`, `sqlite` |
| Productivity | [`paperless-ngx`](templates/paperless-ngx) | `default`, `with-tika-gotenberg` |
| Productivity | [`stirling-pdf`](templates/stirling-pdf) | `default` |
| Productivity | [`vikunja`](templates/vikunja) | `default`, `with-postgres` |
| Media | [`audiobookshelf`](templates/audiobookshelf) | `default` |
| Media | [`calibre-web`](templates/calibre-web) | `default` |
| Media | [`immich`](templates/immich) | `default`, `with-external-postgres` |
| Media | [`jellyfin`](templates/jellyfin) | `default`, `with-hw-transcoding` |
| Media | [`navidrome`](templates/navidrome) | `default` |
| Media Automation | [`arr-stack`](templates/arr-stack) | `default` |
| Media Automation | [`jellyseerr`](templates/jellyseerr) | `default` |
| Networking | [`adguard-home`](templates/adguard-home) | `default` |
| Networking | [`caddy`](templates/caddy) | `default` |
| Networking | [`cloudflared`](templates/cloudflared) | `default` |
| Networking | [`nginx-proxy-manager`](templates/nginx-proxy-manager) | `default`, `with-mariadb` |
| Networking | [`pihole`](templates/pihole) | `default` |
| Networking | [`wireguard-easy`](templates/wireguard-easy) | `default` |
| Monitoring | [`beszel`](templates/beszel) | `default` |
| Monitoring | [`grafana-loki`](templates/grafana-loki) | `default` |
| Monitoring | [`grafana-prometheus`](templates/grafana-prometheus) | `default` |
| Monitoring | [`uptime-kuma`](templates/uptime-kuma) | `default` |
| Development | [`code-server`](templates/code-server) | `default` |
| Development | [`forgejo`](templates/forgejo) | `default`, `with-postgres` |
| Development | [`gitea`](templates/gitea) | `default`, `with-postgres` |
| Home & IoT | [`mosquitto`](templates/mosquitto) | `default` |
| Home & IoT | [`home-assistant`](templates/home-assistant) | `default` |
| Home & IoT | [`node-red`](templates/node-red) | `default` |
| Home & IoT | [`zigbee2mqtt`](templates/zigbee2mqtt) | `default` |
| Content & Web | [`ghost`](templates/ghost) | `default`, `with-mysql` |
| Content & Web | [`homepage`](templates/homepage) | `default` |
| Content & Web | [`linkding`](templates/linkding) | `default` |
| Content & Web | [`mealie`](templates/mealie) | `default` |
| Content & Web | [`wordpress`](templates/wordpress) | `default`, `with-redis` |
| Auth & Security | [`authelia`](templates/authelia) | `default` |
| Auth & Security | [`authentik`](templates/authentik) | `default` |
| Auth & Security | [`vaultwarden`](templates/vaultwarden) | `default`, `with-postgres` |
| Automation & AI | [`n8n`](templates/n8n) | `default`, `with-postgres` |
| Automation & AI | [`ollama-open-webui`](templates/ollama-open-webui) | `default`, `with-gpu` |
| Automation & AI | [`syncthing`](templates/syncthing) | `default`, `host-network` |

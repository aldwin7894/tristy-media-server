# Tristy Media Server

#### ENV vars
```bash
UID=""                    # user id
GID=""                    # group id

JELLYFIN_ROOT_DIR=""      # jellyfin root dir

SHOKO_ROOT_DIR=""         # shoko server root dir

CADDYFILE=""              # caddy config file
CADDY_DATA_DIR=""         # caddy data dir
CADDY_CONFIG_DIR=""       # caddy config dir
CADDY_LOG_DIR=""          # caddy log dir

TAILSCALE_ROOT_DIR=""     # tailscale root dir

ANIME_ROOT_DIR=""         # anime root dir containing library and importing
ANIME_LIBRARY_DIR=""      # anime library dir
MUSIC_LIBRARY_DIR=""      # music dir
```

```bash
docker-compose exec tailscale tailscale up
```

```bash
docker-compose up -d
```

# Tristy Media Server

#### ENV vars

```bash
#======================================================================================
# DOCKER CONFIG
#======================================================================================
PUID=""                         # USER ID
PGID=""                         # GROUP ID
TZ=""                           # TIMEZONE

#======================================================================================
# MEDIA FOLDERS
#======================================================================================
MUSIC_LIBRARY_DIR=""            # MUSIC LIBRARY DIR
PODCAST_ROOT_DIR=""             # PODCAST ROOT DIR
VIDEO_ROOT_DIR=""               # VIDEO ROOT DIR
ANIME_ROOT_DIR=""               # ANIME ROOT DIR CONTAINING DROP SOURCE AND DESTINATION
ANIME_IMPORTING_DIR=""          # ANIME TO BE PROCESSED AND IMPORTED BY SHOKO
ANIME_PRIVATE_DIR=""            # ANIME PRIVATE DIR
ANIME_MOVIE_PRIVATE_DIR=""      # ANIME MOVIE PRIVATE DIR
ANIME_PUBLIC_DIR=""             # ANIME PUBLIC DIR
ANIME_MOVIE_PUBLIC_DIR=""       # ANIME MOVIE PUBLIC DIR

#======================================================================================
# MEDIA SERVER, METADATA AND DOWNLOADER
#======================================================================================
JELLYFIN_ROOT_DIR=""            # JELLYFIN ROOT DIR
JELLYFIN_WEB_DIR=""             # TEST PRE-RELEASE WEB
JELLYFIN_FFMPEG_DIR=""          # TEST PRE-RELEASE FFMPEG

NAVIDROME_ROOT_DIR=""           # NAVIDROME ROOT DIR

SHOKO_ROOT_DIR=""               # SHOKO SERVER ROOT DIR

SONARR_ROOT_DIR=""              # SONARR ROOT DIR

RADARR_ROOT_DIR=""              # RADARR ROOT DIR

LIDARR_ROOT_DIR=""              # LIDARR ROOT DIR

PROWLARR_ROOT_DIR=""            # PROWLARR ROOT DIR

QBITTORRENT_ROOT_DIR=""         # QBITTORRENT ROOT DIR

#======================================================================================
# MONITORING AND UTILITIES
#======================================================================================
CLOUDFLARED_ROOT_DIR=""           # CLOUDFLARED ROOT DIR
TUNNEL_TOKEN=""                   # CLOUDFLARE TUNNEL TOKEN

PIHOLE_WEBPASSWORD=""
PIHOLE_ROOT_DIR=""
PIHOLE_DNSMASQ_DIR=""
PIHOLE_MACVLAN_IP=""

UNBOUND_ROOT_DIR=""
UNBOUND_MACVLAN_IP=""

NETDATA_CLAIM_TOKEN=""
NETDATA_CLAIM_URL=""
NETDATA_CLAIM_ROOMS=""
NETDATA_CONFIG_DIR=""
NETDATA_LIB_DIR=""
NETDATA_CACHE_DIR=""

PROMETHEUS_ROOT_DIR=""

GRAFANA_ROOT_DIR=""

DUPLICATI_ROOT_DIR=""
DUPLICATI_BACKUP_DIR=""
DUPLICATI_ROOT_DIR=""

#======================================================================================
# UPDATE NOTIFIER
#======================================================================================
DIUN_DATA_DIR=""
DIUN_CONFIG_FILE=""
```

#### macvlan for PiHole

```bash
sudo docker network create -d macvlan \
        --subnet=${HOST_IP}/24 --gateway=${GATEWAY_IP} \
        -ip-range ${PIHOLE_MACVLAN_IP}/28 \
        -o parent=eno1 \
        --aux-address="myserver=${PIHOLE_MACVLAN_AUX_IP}" \
        macvlan0
```

```bash
# /usr/local/bin/pi-vlan.sh

#!/usr/bin/env bash
ip link add macvlan-shim link eno1 type macvlan mode bridge
ip addr add 192.168.100.150/28 dev macvlan-shim
ip link set macvlan-shim up
ifconfig macvlan-shim
```

`sudo chmod +x /usr/local/bin/pi-vlan.sh`

```ini
# /etc/systemd/system/pi-vlan.service

[Unit]
After=network.target

[Service]
ExecStart=/usr/local/bin/pi-vlan.sh

[Install]
WantedBy=default.target
```

`sudo systemctl enable pi-vlan`

#### Run stack in detached mode

```bash
docker-compose up -d
```

#### Cloudflare Tunnel

```bash
docker compose exec cloudflare_tunnel cloudflared tunnel login
docker compose exec cloudflare_tunnel cloudflared tunnel create <name>
```

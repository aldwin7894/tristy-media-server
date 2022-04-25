# Tristy Media Server

#### ENV vars
```bash
PUID=""                   # USER ID
PGID=""                   # GROUP ID
TZ=""                     # TIMEZONE

ANIME_ROOT_DIR=""         # ANIME ROOT DIR CONTAINING DROP SOURCE AND DESTINATION
ANIME_LIBRARY_DIR=""      # ANIME LIBRARY DIR
MUSIC_LIBRARY_DIR=""      # MUSIC LIBRARY DIR
VIDEO_ROOT_DIR=""         # VIDEO ROOT DIR
PODCAST_ROOT_DIR=""       # PODCAST ROOT DIR

SHOKO_ROOT_DIR=""         # SHOKO SERVER ROOT DIR

JELLYFIN_ROOT_DIR=""      # JELLYFIN ROOT DIR
JELLYFIN_WEB_DIR=""       # TEST PRE-RELEASE WEB
JELLYFIN_FFMPEG_DIR=""    # TEST PRE-RELEASE FFMPEG

CADDYFILE=""              # CADDY CONFIG FILE
CADDY_DATA_DIR=""         # CADDY DATA DIR
CADDY_CONFIG_DIR=""       # CADDY CONFIG DIR

TAILSCALE_ROOT_DIR=""     # TAILSCALE ROOT DIR

SONARR_ROOT_DIR=""

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

GONIC_ROOT_DIR=""
GONIC_CACHE_DIR=""

QBITTORRENT_ROOT_DIR=""
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

#### Tailscale auth
```bash
docker-compose exec tailscale tailscale up
```

#### Run stack in detached mode
```bash
docker-compose up -d
```

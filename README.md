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
MUSIC_ROOT_DIR=""               # MUSIC ROOT DIR
MUSIC_DOWNLOAD_DIR=""           # MUSIC DOWNLOAD DIR
PODCAST_ROOT_DIR=""             # PODCAST ROOT DIR
VIDEO_ROOT_DIR=""               # VIDEO ROOT DIR
ANIME_ROOT_DIR=""               # ANIME ROOT DIR CONTAINING DROP SOURCE AND DESTINATION

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
PROWLARR_ROOT_DIR=""            # PROWLARR ROOT DIR
QBITTORRENT_ROOT_DIR=""         # QBITTORRENT ROOT DIR
HOMARR_ROOT_DIR=""              # HOMARR ROOT DIR

#======================================================================================
# MONITORING AND UTILITIES
#======================================================================================
CLOUDFLARED_ROOT_DIR=""           # CLOUDFLARED ROOT DIR
TUNNEL_TOKEN=""                   # CLOUDFLARE TUNNEL TOKEN
PIHOLE_ROOT_DIR=""
PIHOLE_WEBPASSWORD=""
PIHOLE_DNSMASQ_DIR=""
PIHOLE_MACVLAN_IP=""
UNBOUND_ROOT_DIR=""
UNBOUND_MACVLAN_IP=""
UNBOUND_REDIS_MACVLANIP=""
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
VAULTWARDEN_ROOT_DIR=""
VAULTWARDEN_DOMAIN=""
VAULTWARDEN_ADMIN_TOKEN=""
NEXTCLOUD_CONFIG_DIR=""
NEXTCLOUD_DATA_DIR=""

#======================================================================================
# UPDATE NOTIFIER
#======================================================================================
DIUN_DATA_DIR=""
DIUN_CONFIG_FILE=""

#======================================================================================
# GLUETUN/VPN CONFIG
#======================================================================================
GLUETUN_ROOT_DIR=""
WIREGUARD_PRIVATE_KEY=""
WIREGUARD_ADDRESSES=""
WIREGUARD_PRESHARED_KEY=""
SERVER_REGIONS=""
FIREWALL_VPN_INPUT_PORTS=""
```

#### Run stack in detached mode

```bash
docker compose up -d
```

#### Cloudflare Tunnel <a href="https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/">Docs</a>
For accessing your server on the web without exposing any public port

```bash
docker compose exec cloudflare_tunnel cloudflared tunnel login
docker compose exec cloudflare_tunnel cloudflared tunnel create <name>
```

## PiHole Setup
#### 1. macvlan for PiHole

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
ip addr add ${PIHOLE_MACVLAN_AUX_IP}/28 dev macvlan-shim
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

#### 2. Set PiHole's Upstream DNS to Unbound IP in:
- Settings > DNS > Upstream DNS Servers > Custom 1
- Uncheck other DNS Servers

## Shoko Setup
- Read the docs <a href="https://docs.shokoanime.com/">here</a>, to setup your local library and have them identified for the metadata, that would be used in Jellyfin
- Join their discord <a href="https://discord.gg/vpeHDsg">here</a> if you need assistance

## Jellyfin Setup
- Add `https://raw.githubusercontent.com/ShokoAnime/Shokofin/metadata/dev/manifest.json` to jellyfin's plugin repositories
- Install latest Shokofin Unstable (Shoko) plugin in the catalog
- In the plugins page, open shokofin settings and set it up as desired (Enable Virtual File System if you don't want to organize your files like [how](https://jellyfin.org/docs/general/server/media/shows/) Jellyfin wants it, Shokofin will do that for you)
- Add your library and make sure only `Shoko` is enabled in all of the `Metadata Downloaders` (to prevent mixed metadata being populated)

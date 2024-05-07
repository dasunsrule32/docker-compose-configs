# Docker Compose Configurations

Docker compose configs for popular applications. These configs are known to work with [@Jip-Hop](https://github.com/Jip-Hop)'s [jailmaker](https://github.com/Jip-Hop/jailmaker) project as well.

## How to use
### Dockge
---
Clone this repo to your `/opt/stacks` directory, to clone, just run:
```
cd /opt/stacks && git clone https://github.com/dasunsrule32/docker-compose-configs.git .
```
Then click `Scan Stacks Folder` to import the compose files. You can then manage any of the docker apps from within Dockge.

![image](https://github.com/dasunsrule32/docker-compose-configs/assets/649815/ac40ec57-5b86-4358-ba85-32cfe8c72054)

### docker compose
---
You can run the following command to start it via `docker compose`.
```
mkdir stacks && cd stacks/<app-dir> && docker compose up -d
```
You can stage the `stacks/` directory where you please.

## Configuring applications from these templates

### Templates
---
Each app is in its own directory with it's own `compose.yaml` and `.env` file. These have been configured for simplicity. Each application may have more configuration options available than what is configured in these templates. See below as an example of how your `stacks` directory will look like.
```
tree -a stacks/
stacks/
├── actual-budget
│   ├── compose.yaml
│   └── .env
├── cloudflared
│   ├── compose.yaml
│   └── .env
├── drawio
│   ├── compose.yaml
│   └── .env
├── homarr
│   ├── compose.yaml
│   └── .env
├── home-assistant
│   ├── compose.yaml
│   └── .env
├── kasm
│   ├── compose.yaml
│   └── .env
├── nginx-proxy-manager
│   ├── compose.yaml
│   └── .env
├── pgadmin
│   ├── compose.yaml
│   └── .env
├── plex
│   ├── compose.yaml
│   └── .env
└── vaultwarden
    ├── compose.yaml
    └── .env
```

### Environment Variables
---
In regards to `ENV` variables within the docker images, you can use the `.env` files or you can use the `environment:` directive in the `compose.yaml` files. An example of using the `environment:` directive would be:
```
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
```
The syntax differs a bit in `.env` files, that same config would looke like:
```
NVIDIA_VISIBLE_DEVICES=all
```
In my example templates, you'll see that each application has various environment variables set for config and data locations, etc. I will use `.env` files, as I prefer secrets, file paths, etc to be separate from the `compose.yaml`.

### Working with templates
---
I will use my `plex` template as an example here. You can see the main body of the `compose.yaml` here. Also notice that I've set the volume paths to my custom paths in a var inside my `.env` file.

`compose.yaml`:
```
services:
  plex:
    container_name: plex
    image: plexinc/pms-docker
    restart: unless-stopped
    network_mode: host
    volumes:
      - ${CONFIG_PATH}:/config
      - ${TRANSCODE_PATH}:/transcode
      - ${MEDIA_PATH}:/data
    runtime: nvidia
    deploy:
      resources:
        reservations:
          devices:
            - capabilities:
                - gpu
    env_file:
      - .env
networks: {}
```

`.env` file:
```
TZ=America/Chicago
PLEX_CLAIM=claim-*******************
NVIDIA_VISIBLE_DEVICES=all
NVIDIA_DRIVER_CAPABILITIES=compute,video,utility
CONFIG_PATH=/mnt/data/plex/configs
TRANSCODE_PATH=/mnt/data/plex/transcode
MEDIA_PATH=/mnt/media/plex
```
All you would need to do is adjust your PATH's and enter your `PLEX_CLAIM`, adjust your timezone. If you don't need nvidia gpu, just comment out those ENV variables and the compose bits for `runtime` and the `deploy` bits for the gpu. After that, you should be off and running.

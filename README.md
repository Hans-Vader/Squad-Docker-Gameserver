[![Discord](https://img.shields.io/discord/747067734029893653)](https://discord.gg/7ntmAwM)
# Supported tags and respective `Dockerfile` links
-	[`latest`, `trixie` (*trixie/Dockerfile*)](https://github.com/Hans-Vader/Squad-Docker-Gameserver/blob/master/trixie/Dockerfile)
-	[`bullseye` (*bullseye/Dockerfile*)](https://github.com/Hans-Vader/Squad-Docker-Gameserver/blob/master/bullseye/Dockerfile)

# What is Squad?
Squad is a tactical FPS that provides authentic combat experiences through teamwork, communication, and gameplay. It seeks to bridge the large gap between arcade shooter and military simulation. Large scale, combined arms combat, base building, and a great integrated VoIP system. <br/>
This Docker image contains the dedicated server of the game. <br/>

> [Squad](http://store.steampowered.com/app/393380/Squad/)

<img src="https://vignette.wikia.nocookie.net/squadgame/images/2/27/Squad_logo.png/revision/latest?cb=20150625185705" alt="logo" width="300"/></img>

# Building the image
The image is not published to a registry, build it locally:
```console
$ docker build -t squad:trixie trixie/
```
For the Debian bullseye variant build `bullseye/` and tag it `squad:bullseye`.

The container runs as uid/gid 1000. If your host account differs, remap it at build time so the
bind-mounted data directory stays owned by you:
```console
$ docker build --build-arg PUID=$(id -u) --build-arg PGID=$(id -g) -t squad:trixie trixie/
```

# How to use this image

## Hosting a simple game server
Running on the *host* interface (recommended):<br/>
```console
$ docker run -d --net=host -v /home/steam/squad-dedicated/ --name=squad-dedicated squad:trixie
```

Running using a bind mount for data persistence on container recreation:
```console
$ mkdir -p $(pwd)/squad-data
$ docker run -d --net=host -v $(pwd)/squad-data:/home/steam/squad-dedicated/ --name=squad-dedicated squad:trixie
```
Create the directory yourself — if dockerd has to create it, it lands root-owned and the container cannot write. Its owner has to match the image's `PUID`/`PGID` (1000:1000 by default, see above).

Running multiple instances (iterate PORT, QUERYPORT, RCONPORT and BEACONPORT):<br/>
```console
$ docker run -d --net=host -v /home/steam/squad-dedicated2/ -e PORT=7788 -e QUERYPORT=27166 -e RCONPORT=21115 -e BEACONPORT=15001 --name=squad-dedicated2 squad:trixie
```

**It's also recommended using "--cpuset-cpus=" to limit the game server to a specific core & thread.**<br/>
**The container will automatically update the game on startup, so if there is a game update just restart the container.**

### docker-compose.yml example
See [docker-compose.example.yml](docker-compose.example.yml) — it builds the image itself, so no registry is needed. All settings live in `.env`, which compose reads automatically:
```console
$ cp docker-compose.example.yml docker-compose.yml
$ cp .env.example .env          # edit ports, server name and mods here
$ id -u; id -g                  # not 1000:1000? put them in .env as PUID/PGID
$ mkdir -p squad-data           # dockerd would create it root-owned
$ docker compose up -d --build
```
The compose file carries its own defaults for every variable, so it comes up without a `.env` too - but those defaults shadow the image ENV, keep them in sync with the Dockerfiles. Run `docker compose config` to see what the server will actually get.

# Configuration
## Environment Variables
Feel free to overwrite these environment variables, using -e (--env):
```dockerfile
PORT=7787
QUERYPORT=27165
BEACONPORT=15000
RCONPORT=21114
FIXEDMAXPLAYERS=100
FIXEDMAXTICKRATE=64
RANDOM=NONE
MODS="()"
SERVER_NAME="Squad Dedicated Server"

MULTIHOME=x.x.x.x - use only if you have multiple IP addresses enter server IP instead of x.x.x.x
```

## Config
The config files can be edited using this command:

```console
$ docker exec -it squad-dedicated nano /home/steam/squad-dedicated/SquadGame/ServerConfig/Server.cfg
```

If you want to learn more about configuring a Squad server check this [documentation](https://squad.gamepedia.com/Server_Configuration).

## Mods

Add each id to the MODS environment variable, for example `MODS="(13371337 12341234 1111111)"`

> MODS must be a bash array `(mod1id mod2id mod3id)` where each mod id is separated by a space and inclosed in brackets

You can get the mod id from the workshop url or by installing it locally and lookup the numeric folder name at `<root_steam_folder>/steamapps/workshop/content/393380`.

# Contributors
[![Contributors Display](https://badges.pufler.dev/contributors/CM2Walki/Squad?size=50&padding=5&bots=false)](https://github.com/CM2Walki/Squad/graphs/contributors)

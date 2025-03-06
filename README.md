# docker-skies-adsb

 Docker container for Skies ADSB visualization

- [docker-skies-adsb](#docker-skies-adsb)
  - [Introduction](#introduction)
  - [Status of this container](#status-of-this-container)
  - [Installation](#installation)
  - [Configuration](#configuration)
    - [Parameter configuration](#parameter-configuration)
    - [Port configuration](#port-configuration)
    - [Volume configuration](#volume-configuration)
  - [License](#license)

## Introduction

This container is a wrapper for [skies-adsb](https://github.com/machineinteractive/skies-adsb), which is incorporated herein. It exposes basic functions only. For documentation on what this container does, please follow the link above.

## Status of this container

I implemented this container as a "science project"; mainly to help satisfy the curiosity of several members of the [SDR-Enthusiasts group on Discord](https://discord.gg/zpwX2Y6zUK). The idea was to evaluate Skies-ADSB in a containerized fashion, allowing the user to quickly launch (or abandon) Skies-ADSB on any Debian Linux machine, including Raspberry Pi and Linux PC.

Based on user feedback, we'd then decide to "fully support", "park", "abandon" this container.

After polling users, we have come to the conclusion that we'll "park" the effort for now. As such, this container is **fixed** using [Skies-ADSB tag 2.3.2](https://github.com/machineinteractive/skies-adsb/tree/7511ff934b02a90f1095c322e4b8b3d73bcc5d56), which ensures that the container will continue  to build and function for the foreseeable future. However, any improvements that follow v2.3.2 won't be included.

If the interest increases (go to the [#skies-adsb](https://discord.gg/zpwX2Y6zUK) channel on the SDR-E Discord server to express your opinion), we can revisit this decision.

## Installation

At the moment, we don't have a prebuilt multi-architecture image, but you can easily build the container locally by using the setup shown in the accompanying [docker-compose.yml](docker-compose.yml).

You can then start the container with this command:

```bash
docker compose build  # builds the container from source
docker compose up -d  # starts the container
```

If you want to rebuild the container in the future (for example, because there are updates to the container or to the underlying `skies-adsb` software that you want to deploy), simply repeat the commands above.

When you run the container for the first time, it will download a bunch of files  from the internet and use them to create vector and shapefiles. This may take a long time (5 - 10 minutes). If you correctly map the directories as we suggest below, then this will be a one-time thing: these files would only be re-downloaded / reprocessinged if you change the LAT/LON settings, or if the resulting files somehow were lost.

## Configuration

A complete example of the configuration can be found in the [docker-compose.yml](docker-compose.yml) file in this repository. This section explains the parameters, ports, and volumes that are used.

### Parameter configuration

The following parameters must be set:

| Parameter | Description |
|-----------|-------------|
| `LAT`     | Latitude of the receiver in decimal degrees |
| `LON` (or `LONG`) | Longitude of the receiver in decimal degrees |
| `SBS_SOURCE` | SBS source in the form of `host_or_ip:port`. Forexample: `ultrafeeder:30003` |

### Port configuration

You should expose the following ports:

| Port | Description |
|------|-------------|
| `5173` | Port on which the web interface will be reachable |
| `5000` | This port must be reachable from the browser - the browser will connect to this port using Websockets to dynamically receive the aircraft data |

### Volume configuration

In order to keep the vector, shapefile, and map data between rebuilds of the container, we strongly recommend mapping the following directories to a persistent volume. If you don't do this, then the container will rebuild the vector, shapefiles, and maps every time you rebuild the container, which will take a very long time.

| Directory to be mapped | Content of this directory |
|------------------------|---------------------------|
| `/skies-adsb/maps/data` | Raw map data, shapefiles, etc. |
| `/skies-adsb/public/map-data` | Processed map data, vectors, and shapefiles |

## License

The software contained in the repository is a wrapper around [skies-adsb](https://github.com/machineinteractive/skies-adsb), which is licensed under the [GNU Public License, version 3](LICENSE). It is also Copyright 2025 by Ramon F. Kolb, kx1t.

[skies-adsb](https://github.com/machineinteractive/skies-adsb) is licensed using the MIT license, which you can find at the link above.

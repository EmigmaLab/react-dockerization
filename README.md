# React Dockerization

## Stack

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Node.js](https://img.shields.io/badge/node.js-6DA55F?style=for-the-badge&logo=node.js&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-%23000000.svg?style=for-the-badge&logo=nextdotjs&logoColor=white)
![Docker Hardened Images](https://img.shields.io/badge/DHI-%23000.svg?style=for-the-badge&logo=docker&logoColor=white)
![Alpine Linux](https://img.shields.io/badge/Alpine_Linux-%230D597F.svg?style=for-the-badge&logo=alpine-linux&logoColor=white)

## Maintainers

![Gregor Vojvoda](https://img.shields.io/badge/Gregor%20Vojvoda-%2324292e.svg?style=for-the-badge&logo=github&logoColor=white) [gregor@emigma.com](mailto:gregor@emigma.com)

![Luka Sotosek](https://img.shields.io/badge/Luka%20Sotosek-%2324292e.svg?style=for-the-badge&logo=github&logoColor=white) [luka@emigma.com](mailto:luka@emigma.com)

![Aljaž Neuberg](https://img.shields.io/badge/Aljaž%20Neuberg-%2324292e.svg?style=for-the-badge&logo=github&logoColor=white) [aljaz@emigma.com](mailto:aljaz@emigma.com)

## Overview

Generic Docker setup for Next.js frontend apps. **do not customize them**. All configuration goes in `.env`.

Files:

- `Dockerfile` — multi-stage build on DHI Node Alpine (install → build → standalone run).
- `docker-compose.yml` — generic `fe` service.
- `example.env` — template with every supported parameter (committed).
- `.env` — the **only** file you edit per project.

Prereqs per app: Next.js with `output: "standalone"`, a lockfile (`package-lock.json` / `yarn.lock` / `pnpm-lock.yaml`).

## Node versions

| Node | Image |
|---|---|
| 26 | `dhi.io/node:26-alpine-dev@sha256:…` |

Branch naming: `node-<ver>` (e.g. `node-26`). Use the branch that matches the Node major the app should run on.

## Configuration (`.env`)

1. Copy the template env in your React project:

   ```sh
   cp example.env .../.../.env
   ```

2. Set values. App-specific secrets/URLs go in the same file — request real values from a [maintainer](#maintainers).

How `.env` is used:

- **Compose interpolation** — Compose loads `.env` automatically for `${IMAGE_NAME}`, `${HOST_PORT}`, …
- **Container environment** — `env_file: .env` injects the whole file into the container


## Usage - Locally

```sh
docker compose up -d --build
docker compose config   # inspect resolved values
docker compose ps -a
docker compose down
```

App URL: `http://<BIND_ADDRESS>:<HOST_PORT>` (e.g. `http://127.0.0.1:9500`).

## Parameters

All keys are set in `.env`. See `example.env` for example values.

### Docker / Compose

| Name | Default | Notes |
|---|---|---|
| `IMAGE_NAME` | **required** | Docker image name (e.g. `nusz2026-fe`) |
| `HOST_PORT` | **required** | Port on the host |
| `APP_PORT` | `3000` | Port inside the container |
| `BIND_ADDRESS` | `127.0.0.1` | Localhost only (reverse proxy in front) |

Mapping: `BIND_ADDRESS:HOST_PORT` → `APP_PORT`.

### Application

Any other key is passed through to the container unchanged, e.g.:

| Name | Notes |
|---|---|
| `DIRECT_URL - REQUIRED`| Backend / app base URL |
| `KEYCLOAK_BASE_URL - OPTIONAL` | Keycloak server |
| `KEYCLOAK_REALM - OPTIONAL ` | Realm name |
| `KEYCLOAK_CLIENT_ID - OPTIONAL` | Client id |
| `KEYCLOAK_CLIENT_SECRET - OPTIONAL` | Client secret |
| `Others...` | / |

Names depend on the app — keep them in `.env` only, never in `docker-compose.yml`.

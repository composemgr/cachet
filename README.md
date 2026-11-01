## 👋 Welcome to Cachet 🚨

Open-source status page system for service health and incident reporting

## 📋 Description

Cachet is an open-source status page application that shows the health of your
services and records incidents. It runs as a Laravel app against PostgreSQL,
with the admin created on first boot. This stack runs the official container
image, keeps the application data under `./volumes/data/cachet` and the
configuration under `./volumes/config/cachet`, and publishes only the web UI.

## 🚀 Services

- **app**: ghcr.io/cachethq/cachet:latest

### Infrastructure Components

- **db**: postgres:latest, PostgreSQL database

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/cachet/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/cachet" ~/.local/srv/docker/cachet
cd ~/.local/srv/docker/cachet
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install cachet
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
APP_ENV=production
APP_DEBUG=false
APP_SECRET_KEY=replace-with-a-random-32-byte-hex-string
DB_CREATE_DATABASE_NAME=cachet
DB_USER_NAME=cachet
DB_USER_PASS=replace-with-a-random-password
EMAIL_SERVER_HOST=172.17.0.1
EMAIL_SERVER_PORT=587
EMAIL_SERVER_LOGIN_NAME=
EMAIL_SERVER_LOGIN_PASS=
EMAIL_SERVER_MAIL_FROM=noreply@example.com
```

`APP_SECRET_KEY` becomes the Laravel `APP_KEY`, it encrypts sessions and the
component credentials Cachet stores.

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64128` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

Only the `app` service carries the proxy labels and joins the external
network. The database stays on the internal project network.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64128

## 📂 Volumes

- `./volumes/data/cachet` - Application data, mounted at `/data`
- `./volumes/config/cachet` - Application configuration, mounted at
  `/config`
- `./volumes/db/postgres/cachet` - Database data

## 🔐 Security

- Change `DB_USER_PASS` before the first start, it is the PostgreSQL password
  the app connects with
- Set `APP_SECRET_KEY` to a random 32 character or longer value, it is the
  Laravel application key
- Keep `APP_DEBUG=false` in production, debug mode leaks stack traces and the
  environment
- Set the `EMAIL_SERVER_LOGIN_*` variables if you want Cachet to send
  subscriber notifications
- The status page is meant to be public, so use the reverse proxy in front of
  it and do not publish it wider than you need
- Regularly update Docker images for security patches
- Back up `./volumes/db/postgres/cachet`, it holds components and incidents

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄

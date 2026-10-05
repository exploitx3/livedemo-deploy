# LiveDemo: open-source interactive demo platform

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Website](https://img.shields.io/badge/website-livedemo.ai-4f46e5)](https://livedemo.ai)

LiveDemo is an open-source (MIT) platform for building interactive product demos. Capture a product flow, edit it into a clickable demo, then share it as a link, embed it on your site, or let an AI agent guide buyers through it.

Looking for an **open-source alternative to [Navattic](https://livedemo.ai/compare/navattic/), [Storylane](https://livedemo.ai/compare/storylane/) or [Arcade](https://livedemo.ai/compare/arcade/)**? This repository runs the full LiveDemo stack on your own machine or server with Docker Compose. Self-hosting is free.

![Screenshot 1](screenshots/1.png)

---

## Features

- **Capture**: Chrome extension, Figma plugin, macOS and Windows apps (HTML, screenshot and video demos)
- **Edit**: steps, popups, pan and zoom, autoplay, call-to-actions, brand design
- **AI**: AI voiceover, AI text personalization and Agentic Demos, where an AI agent guides the buyer and answers questions (bring your own API keys)
- **Share**: public link, website embed (iframe), email, GIF and MP4 export
- **Analytics**: views, completion rate, drop-off, time spent and captured leads

![Screenshot 2](screenshots/2.png)

![Screenshot 3](screenshots/3.png)

---

## Self-host or use the cloud

| Option | Cost | Best for |
|---|---|---|
| **Self-host** (this repo) | Free, MIT license | Teams that want full control over their data and infrastructure |
| **[LiveDemo Cloud](https://app.livedemo.ai/register)** | Free plan for 1 creator, paid plans from $34/mo | Teams that want to start in minutes without running servers |
| **[Business plan](https://livedemo.ai/#pricing)** | Custom, from 10 creators | Whitelabel, custom domain and dedicated support, hosted by LiveDemo or by you |

Compare LiveDemo with 26 other demo tools: [livedemo.ai/compare](https://livedemo.ai/compare/)

---

## Source code

| Repository | Description |
|---|---|
| [livedemo-backend](https://github.com/exploitx3/livedemo-backend) | Backend API and consumer |
| [livedemo-web-app](https://github.com/exploitx3/livedemo-web-app) | Demo editor web app |
| [livedemo-chrome-app](https://github.com/exploitx3/livedemo-chrome-app) | Chrome extension for capturing demos |
| [livedemo-web-proxy](https://github.com/exploitx3/livedemo-web-proxy) | Web proxy |
| [livedemo-ai-api](https://github.com/exploitx3/livedemo-ai-api) | AI API |
| [livedemo-rrweb](https://github.com/exploitx3/livedemo-rrweb) | Fork of rrweb for recording and replaying the web |

---

## Quick start

This deployment runs three containers:

- **mongo**: MongoDB 8 on port `27017`, with a persistent local volume
- **livedemo-backend**: backend API on port `3005`
- **livedemo-web-app**: editor web app on port `5000`

All images are pulled from Docker Hub (`docker.io/livedemo/...`), so no local build is required. The containers use host networking (`network_mode: host`).

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed and running
- [Docker Compose](https://docs.docker.com/compose/install/) v2+ (`docker compose` command)

### 1. Configure environment variables

Two env files are included as templates in `local/envs/`. Fill in any secrets or keys you need before starting.

**`local/envs/backend.env`**: variables for `livedemo-backend`

| Variable | Description |
|---|---|
| `PRIVATE_AUTH_TOKEN` | Auth token for internal service calls |
| `OPENAI_API_KEY` | OpenAI API key (needed for AI features) |
| `STRIPE_SECRET_KEY` | Stripe secret key (use `sk_test_` for dev) |
| `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` | AWS credentials (optional for dev) |
| `ELEVENLABS_API_KEY` | ElevenLabs key (optional, for AI voiceover) |
| `MUX_TOKEN_ID` / `MUX_TOKEN_SECRET` | Mux credentials (optional, for video) |

> `DB_URI` is pre-configured to point to the `mongo` container. Do not change it.

**`local/envs/web-app.env`**: variables for `livedemo-web-app`

| Variable | Description |
|---|---|
| `STRIPE_PUBLISHABLE` | Stripe publishable key (use `pk_test_` for dev) |
| `CAPTCHA_SITE_KEY` | reCAPTCHA site key (optional) |
| `GOOGLE_FONT_API_KEY` | Google Fonts API key (optional) |

### 2. Pull the latest images

```bash
docker compose -f local/docker-compose.yml pull
```

### 3. Start all containers

```bash
docker compose -f local/docker-compose.yml up -d
```

This will:
1. Start MongoDB and wait until it passes its health check
2. Start `livedemo-backend` once MongoDB is healthy
3. Start `livedemo-web-app` once the backend is up

---

## Accessing the services

| Service | URL |
|---|---|
| Web app | http://localhost:5000 |
| Backend API | http://localhost:3005 |
| MongoDB | mongodb://localhost:27017 |

---

## Common commands

```bash
# View logs for all containers
docker compose -f local/docker-compose.yml logs -f

# View logs for a specific container
docker compose -f local/docker-compose.yml logs -f livedemo-backend
docker compose -f local/docker-compose.yml logs -f livedemo-web-app

# Stop all containers (data is preserved)
docker compose -f local/docker-compose.yml down

# Stop and remove all data volumes (full reset)
docker compose -f local/docker-compose.yml down -v

# Restart a single container
docker compose -f local/docker-compose.yml restart livedemo-backend

# Pull latest images and recreate containers
docker compose -f local/docker-compose.yml pull && docker compose -f local/docker-compose.yml up -d
```

---

## Data persistence

MongoDB data and backend uploaded files are stored in named Docker volumes. Compose prefixes them with the project name, which defaults to `local` (the folder containing `docker-compose.yml`):

| Volume | Contents |
|---|---|
| `local_mongo_data` | MongoDB database files |
| `local_backend_data` | Demos, stories, and story request files |

To list volumes:

```bash
docker volume ls | grep -E 'mongo_data|backend_data'
```

To remove volumes and start fresh:

```bash
docker compose -f local/docker-compose.yml down -v
```

---

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) and the [Code of Conduct](CODE_OF_CONDUCT.md). Report security issues as described in [SECURITY.md](SECURITY.md).

---

## License

MIT License (see [`LICENSE`](LICENSE)).

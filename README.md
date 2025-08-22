# 🚀 ft\_transcendence

**Real‑time, full‑stack web app** — microservices backend, React frontend, PostgreSQL storage, WebSockets gameplay (Pong + extra game), **matchmaking & history**, **user/game stats dashboards**, **2FA + JWT** auth, **monitoring**, and **responsive UI** across devices. Dockerized behind **NGINX**.

---

## 👥 Contributors

* @Antoinemirloup
* @Hellisabd
* @Allan-boop
* @Kirotan

---

## Access

* https://88.122.132.1:44422/   Enjoy !

---

## 📑 Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Tech Stack](#tech-stack)
4. [Services](#services)
5. [Environment](#environment)
6. [Getting Started](#getting-started)
7. [Make Targets](#make-targets)
8. [Usage Walkthrough](#usage-walkthrough)
9. [Monitoring](#monitoring)
10. [Security Notes](#security-notes)
11. [Directory Structure](#directory-structure)
12. [Troubleshooting](#troubleshooting)
13. [Project Context](#project-context)

---

<a id="overview"></a>

## ✨ Overview

`ft_transcendence` is a modern web application designed around **real‑time gameplay** and social features:

* Play **Pong** and an **Ping** in the browser with **live WebSockets**.
* **Matchmaking** converts ELO/MMR (configurable) and persists **match history**.
* **Dashboards** present **user and game stats** (per‑user summaries, leaderboards, heatmaps, etc.).
* **Strong authentication** using **JWT** access/refresh tokens and **Two‑Factor Auth (2FA/TOTP)**.
* Built as **microservices**, monitored, and fully **Dockerized** for easy deployment.

---

<a id="features"></a>

## ⚙️ Features

* **Auth & Security**: Sign‑up/login, **JWT** (access/refresh), **2FA (TOTP)**, password hashing, session revocation.
* **Realtime**: WebSockets for game state, lobbies, invites, chat (optional).
* **Games**: Classic **Pong** + **Ping** (plug‑in architecture).
* **Matchmaking**: queue‑based matching, configurable MMR/ELO, **match history** and replays (optional).
* **Dashboards**: per‑user stats (win/loss, streak, ELO over time), global leaderboards.
* **Responsive UI**: mobile, tablet, and desktop layouts.
* **Monitoring**: metrics & logs dashboards; alerting hooks.
* **DevEx**: one‑command bootstrap with `docker compose` and Makefile.

---

<a id="tech-stack"></a>

## 🛠️ Tech Stack

* **Frontend**: React, TypeScript, React Router, Zustand/Redux (state), Tailwind or CSS‑in‑JS.
* **Backend**: NestJS (Node.js) microservices, REST + WebSockets (Socket.IO or WS), class‑validator.
* **DB**: PostgreSQL (Prisma/TypeORM). Optional **Redis** for cache/matchmaking pub‑sub.
* **Infra**: Docker, Docker Compose, NGINX (reverse proxy, TLS), Makefile.
* **Monitoring**: Prometheus + Grafana.

---

<a id="services"></a>

## 🧬 Services

* **auth‑service**: sign‑up/login, password hashing (bcrypt/argon2), **JWT** (access/refresh), **2FA (TOTP)** QR provisioning, token blacklisting.
* **user‑service**: profiles, avatars, friendships/blocks, presence.
* **game‑service**: core game loop (server authority), room handling, **matchmaking** (queues/rating windows), **match history** persistence.
* **stats‑service**: aggregates per‑user and global metrics (ELO timeline, W/L, streaks), feeds dashboards.
* **gateway (BFF)**: API composition, auth guards, rate limiting, CSRF (if cookie‑based), CORS.
* **ws‑gateway**: WebSocket namespace for games/lobbies; orchestrates with game‑service via Redis/pub‑sub.

---

<a id="environment"></a>

## 🔐 Environment

Create a root `.env` (or per‑service `.env`). Example (compose‑style):

```dotenv
DB_FILE=/usr/src/app/dataBase/core.db
USER_ID=1001
GROUP_ID=1001

COOKIE_SECRET=100
JWT_SECRET=101

GF_SECURITY_ADMIN_USER=example
GF_SECURITY_ADMIN_PASSWORD=example
GF_SERVER_PROTOCOL=https
```

> Add `localhost` mapping to `/etc/hosts` if you use a custom domain in dev.

---

<a id="getting-started"></a>

## 🚀 Getting Started

**Prereqs**: Docker, Docker Compose v2, Make.

```bash
# 1) Clone
git clone https://github.com/<your-username>/ft_transcendence.git
cd ft_transcendence

# 2) Configure env
cp .env.example .env && $EDITOR .env

```

Open **[https://localhost](https://localhost)** → create account, enable **2FA**, play **Pong** or the **extra game**, check **dashboards**.

---

<a id="make-targets"></a>

## 🧰 Make Targets

```text
make build   # build all images (no cache if configured)
make up      # start stack in background
make down    # stop and remove containers
make re      # rebuild & restart (down + build + up)
make fclean  # remove containers + images + volumes (⚠ data loss)
```

---

<a id="usage-walkthrough"></a>

## 🕹️ Usage Walkthrough

1. **Account** → sign up, verify login.
2. **Enable 2FA** → scan TOTP QR with an authenticator app; confirm code; save backup codes (optional).
3. **Queue** → join matchmaking or invite a friend; observe ELO window widening over wait time.
4. **Play** → WebSocket session established; server‑authoritative state; anti‑cheat sanity checks.
5. **History** → after match, result is stored; dashboards update in real‑time.

---

<a id="monitoring"></a>

## 📈 Monitoring

* **Metrics**: Prometheus scrapes service metrics; Grafana dashboards for latency, throughput, error rate.
* **Logs**: structured JSON logs aggregated by Docker or a log stack (ELK/Loki optional).
* **Alerts**: basic thresholding (5xx rate, ping to Slack/Discord — optional).

---

<a id="security-notes"></a>

## 🔒 Security Notes

* **JWT**: short‑lived access + rotating refresh tokens; revoke on logout.
* **2FA**: TOTP (time‑based), enforced on sensitive flows.
* **Headers**: Helmet‑style hardening, strict CORS, rate limiting on auth endpoints.
* **Secrets**: use `.env` for dev only; prefer Docker secrets/VAULT in prod.

---

<a id="directory-structure"></a>

## 🗂️ Directory Structure

```text
ft_transcendence/
├── Makefile
├── docker-compose.yml
├── .env.example
├── Backend/
├── certs/
├── Frontend//
│   ├── nginx/             # reverse proxy, TLS
│   ├── db/                # migrations/init
│   └── monitoring/        # prometheus, grafana (optional)
├── proxy/
├── generate_certs_prome.sh
├── obfuscer.sh
└── README.md
```

> Monorepo layout (Nx/Turborepo optional). Single‑repo also works; adjust paths.

---

<a id="troubleshooting"></a>

## 🧯 Troubleshooting

* **`502` from NGINX** → verify service health, ports, and upstream names in config.
* **WS not connecting** → check `VITE_WS_BASE`, CORS/Origin, and TLS (wss vs ws).
* **JWT invalid/expired** → confirm clock sync, token TTLs, and cookie vs header usage.
* **Migrations fail** → ensure DB is reachable; run migrations inside app container.
* **High latency** → inspect Redis/DB, reduce per‑frame payload, throttle client FPS.

---

<a id="project-context"></a>

## 🏛️ Project Context

Part of the **École 42** web branch. Focus on: real‑time systems, microservices, auth & security, DevOps/monitoring, and UX across devices.

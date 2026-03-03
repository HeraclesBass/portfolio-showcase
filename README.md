# Hercules Platform

Production microservices infrastructure running **77 Docker containers** across **33 compose projects** on a single Linux server — with SSO authentication, encrypted secrets, automated backups, real-time streaming, and full observability across every service.

**Live at [herakles.dev](https://herakles.dev)** | [hello@herakles.dev](mailto:hello@herakles.dev)

## Architecture

```
                     ┌──────────────────────────────────────────────┐
                     │              nginx gateway                    │
                     │   72 site configs, 67 SSL, 39 auth-protected │
                     │   rate limiting, security filtering, WSS     │
                     └──────────────────────┬───────────────────────┘
                                            │
          ┌─────────────────┬───────────────┼───────────────┬──────────────────┐
          │                 │               │               │                  │
   ┌──────┴──────┐  ┌──────┴──────┐ ┌──────┴──────┐ ┌──────┴──────┐  ┌───────┴───────┐
   │  Frontends   │  │  APIs       │ │  Workers    │ │  AI/ML      │  │  Infra        │
   │  Next.js     │  │  FastAPI    │ │  Celery     │ │  LLM Gate   │  │  Authelia SSO │
   │  React       │  │  Express    │ │  GPU Lambda │ │  Ollama     │  │  Vault        │
   │  11 apps     │  │  Flask      │ │  Scrapers   │ │  Embeddings │  │  K8s dev      │
   └──────┬──────┘  └──────┬──────┘ └──────┬──────┘ └──────┬──────┘  └───────┬───────┘
          │                 │               │               │                  │
          └─────────────────┴───────────────┼───────────────┴──────────────────┘
                                            │
          ┌─────────────────┬───────────────┼───────────────┬──────────────────┐
          │                 │               │               │                  │
   ┌──────┴──────┐  ┌──────┴──────┐ ┌──────┴──────┐ ┌──────┴──────┐  ┌───────┴───────┐
   │ PostgreSQL   │  │ Redis       │ │ pgvector    │ │ PostGIS     │  │ ChromaDB      │
   │ 8 instances  │  │ 3 instances │ │ embeddings  │ │ geospatial  │  │ vector store  │
   └──────┬──────┘  └──────┬──────┘ └──────┬──────┘ └──────┬──────┘  └───────┬───────┘
          │                 │               │               │                  │
   ┌──────┴─────────────────┴───────────────┴───────────────┴──────────────────┴───────┐
   │  Observability: Prometheus (31 targets) → Grafana │ Loki ← Promtail │ OTel │ SMS  │
   └───────────────────────────────────────────────────────────────────────────────────┘
```

## What's Running

### AI & Machine Learning

| Service | What it does | Architecture |
|---------|-------------|--------------|
| **AI Agent Orchestration** | 104 specialized agents coordinated by meta-orchestrator | V11 framework, 51 skills, 12 lifecycle hooks, 5-level autonomy |
| **Free LLM Gateway** | Centralized API key pooling across 16+ models | OpenAI-compatible proxy, provider failover chains |
| **RAG Libraries** | Semantic search + AI chat over document corpora | FastAPI + pgvector (768-dim HNSW) + Sentence Transformers + K-means clustering |
| **GPU Stem Separation** | Audio source separation with ensemble ML models | 6 models (demucs, roformer, mdx23c), SNR-weighted merging, Lambda A100 via SSH |
| **Co-Processors** | Gemini 3.1 Pro for reasoning, Pi agent for agentic loops | CLI bridges with tier routing, safety extensions, budget tracking |
| **Ollama** | Local LLM inference | Self-hosted, no data leaves the server |

### Web Applications & APIs

| Service | What it does | Architecture |
|---------|-------------|--------------|
| **[FinePrint](https://fine-print.org)** | AI-powered Terms of Service analyzer | Next.js + PostgreSQL + Redis + Gemini, shareable analysis links |
| **Claude Trader** | Crypto trading with AI predictions | Express + FastAPI + PostgreSQL + Socket.IO (7-day persistent WS), Monte Carlo risk analysis |
| **Picture Organizer** | Photo timeline with AI tagging | Next.js + FastAPI + Celery workers + Gemini Vision + DeepFace + Backblaze B2 |
| **ICE-Aware** | Detention facility tracking (1.61M records) | FastAPI + PostGIS + 20 county jail scrapers + 75 API endpoints |
| **Portfolio Platform** | Interactive portfolio with 6 deployed apps | React + Three.js + WebGL shaders, gateway routing |
| **CK Reynolds Tax** | Tax service SaaS (client project) | Next.js + Supabase + Square payments + Twilio SMS, 55 API routes |

### Real-Time & Streaming

| Pattern | Where it's used | Details |
|---------|----------------|---------|
| **WebSocket** | Trading, terminal, GPU viz | Socket.IO with 7-day timeout, binary frame protocol for GPU data |
| **Server-Sent Events** | AI chat, status updates | Streaming LLM responses with proxy_buffering off |
| **HLS Live Streaming** | 3-body simulation | Cinematic N-body physics with spectral audio synthesis |
| **Async Workers** | Photo processing, scraping | Celery task queues, GPU Lambda execution, 20 concurrent scrapers |

## Security

```
Internet → nginx (444 drops unknown hosts) → Authelia SSO → Application
                                                  │
                              ┌────────────────────┼────────────────────┐
                              │                    │                    │
                         5-tier ACL          Argon2id hashing     SMS 2FA
                      (public → admin)      (64MB, 3 iterations)  (Twilio)
```

| Layer | Implementation |
|-------|---------------|
| **Authentication** | Authelia SSO — 5 access tiers (public, free, per-user, premium, admin), 39 protected domains |
| **Password Security** | Argon2id with m=65536, t=3, p=4, 16-byte salt |
| **TLS** | Let's Encrypt across 67 sites, TLS 1.2/1.3, HSTS with 1-year max-age |
| **Request Filtering** | nginx security map drops .env probes, WordPress scanners, SQL injection, phpMyAdmin crawlers (444 response) |
| **Rate Limiting** | Per-endpoint zones (5 req/min on AI analysis, 30 req/min on reads, 3 req/min on uploads) |
| **Secrets** | AES-256-CBC encrypted credential files with PBKDF2 key derivation, HashiCorp Vault, rotation scripts |
| **Headers** | X-Frame-Options, X-Content-Type-Options, HSTS, X-XSS-Protection, Permissions-Policy per domain |
| **Internal Access** | Metrics endpoints restricted to 127.0.0.1 + Docker subnets, health checks bypass auth |

## Observability

| Component | Configuration | Scale |
|-----------|--------------|-------|
| **Prometheus** | 15s scrape interval, 24 job definitions | 31 active targets |
| **Database Exporters** | 4 PostgreSQL + 5 Redis exporters | Per-instance query/connection metrics |
| **Grafana** | Auto-provisioned datasources + dashboards | Service status + infrastructure + security dashboards |
| **Loki** | 7-day retention, 16MB ingestion limit, v11 schema | Centralized logs from all containers |
| **Promtail** | Ships nginx, Authelia, fail2ban, container logs | Security-focused log pipeline |
| **OpenTelemetry** | OTLP gRPC (:4317) + HTTP (:4318) receivers | Distributed tracing with batch processing |
| **Alert Rules** | 11 rules across 3 groups (security, infrastructure, availability) | Credential harvesting, lateral movement, exfiltration, container escape detection |
| **SMS Alerting** | Twilio bridge with rate limiting (12/hour cap) | Critical alerts → phone notification |

## Backup & Disaster Recovery

| Tier | Schedule | Scope |
|------|----------|-------|
| **Critical** | Daily | Platform databases, auth, active projects |
| **Important** | Mon/Wed/Fri | Application data, trading, media |
| **Standard** | Weekly | Full backup with exclusions |

Restic-based with Hetzner offsite storage, Prometheus metrics exporter for backup health, automated restore procedures.

## Nginx Gateway

72 site configurations routing to 30+ backend services:

| Feature | Details |
|---------|---------|
| **Domain routing** | 70+ subdomains on herakles.dev + external domains (iolaus.ai, keymakers.ai, fine-print.org) |
| **Auth integration** | Forward auth via Authelia with selective bypass for health/metrics/public endpoints |
| **WebSocket** | Upgrade handling with 24-hour+ timeouts for trading, terminal, GPU binary frames |
| **GPU fallback** | Upstream routing to remote Hetzner GPU server with automatic local fallback on 502/503/504 |
| **Upload tiers** | 1MB default, 50MB screenshots, 500MB video, 2GB for processing pipelines |
| **SSE tuning** | proxy_buffering off, chunked_transfer_encoding off for streaming AI responses |
| **Caching** | Immutable hashed assets (1 year), no-cache on HTML/manifests, 7-day static resources |

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Languages** | Python, TypeScript, JavaScript, Go, Bash, SQL |
| **Frontend** | React, Next.js, Tailwind CSS, Three.js, WebGL/WGSL, Pixi.js |
| **Backend** | FastAPI, Express, Flask, Node.js, Celery |
| **AI/ML** | Gemini 3.1 Pro, Claude Opus 4.6, Ollama, Sentence Transformers, DeepFace, ensemble audio models |
| **Real-Time** | Socket.IO, WebSocket, SSE, HLS streaming, Redis pub/sub |
| **Databases** | PostgreSQL (8 instances), Redis (3 instances), pgvector, PostGIS, ChromaDB, SQLite |
| **Infrastructure** | Docker (77 containers), nginx, Authelia SSO, HashiCorp Vault, Kubernetes (dev), Restic |
| **Observability** | Grafana, Prometheus, Loki, Promtail, OpenTelemetry, node-exporter, 9 DB exporters |
| **Security** | Authelia (Argon2id + SMS 2FA), Let's Encrypt, AES-256-CBC secrets, fail2ban, rate limiting |

## Metrics

| Metric | Value |
|--------|-------|
| Running containers | 77 |
| Compose projects | 33 |
| Live domains | 70+ (including iolaus.ai, keymakers.ai, fine-print.org) |
| SSL certificates | 67 sites |
| Auth-protected domains | 39 |
| Prometheus scrape targets | 31 |
| Alert rules | 11 (security + infrastructure + availability) |
| Database instances | 8 PostgreSQL + 3 Redis + PostGIS + ChromaDB |
| AI agents | 104 (69 active) |
| Codebase | 390K+ LOC |

## Project Repos

| Project | Description | Stack |
|---------|-------------|-------|
| [tos-analyzer](https://github.com/HeraclesBass/tos-analyzer) | AI-powered Terms of Service analyzer ([live](https://fine-print.org)) | Next.js, TypeScript, Gemini, PostgreSQL, Redis |
| [claude-trader-pro](https://github.com/HeraclesBass/claude-trader-pro) | Crypto trading platform with AI predictions and real-time streaming | Python, FastAPI, React, Socket.IO, PostgreSQL |
| [athenaeum](https://github.com/HeraclesBass/athenaeum) | Self-hosted semantic library platform with RAG chat and MCP server | Python, FastAPI, pgvector, Next.js, Sentence Transformers |
| [3-body-problem](https://github.com/HeraclesBass/3-body-problem) | GPU-accelerated N-body simulation with HLS streaming | Python, NVIDIA Warp, OpenGL |
| [manifold-visualizer](https://github.com/HeraclesBass/manifold-visualizer) | WebGPU mathematical surface renderer ([live](https://manifold.herakles.dev)) | Python, Flask, WebGPU, WGSL |
| [observability-showcase](https://github.com/HeraclesBass/observability-showcase) | Production observability stack with security alerting | Grafana, Prometheus, Loki, OpenTelemetry, fail2ban |
| [claude-orchestrator-showcase](https://github.com/HeraclesBass/claude-orchestrator-showcase) | V11 agent orchestration framework | Agent Teams, 8 formations, lifecycle hooks |
| [iolaus-zeus-showcase](https://github.com/HeraclesBass/iolaus-zeus-showcase) | Dual-interface AI platform — CLI agent + web command center | TypeScript, React, Python, FastAPI, WebSocket |
| [claude-gemini](https://github.com/HeraclesBass/claude-gemini) | CLI bridge delegating reasoning to Gemini 3.1 Pro | Python, Google GenAI SDK |
| [claude-pi](https://github.com/HeraclesBass/claude-pi) | Co-processor CLI for Pi agent with safety firewall | TypeScript, Node.js, Commander.js |
| [ckreynolds-tax-showcase](https://github.com/HeraclesBass/ckreynolds-tax-showcase) | Tax service SaaS platform (client project) | Next.js, TypeScript, Supabase, Square, Twilio |

---

Built and operated by [D. Michael Piscitelli](https://github.com/HeraclesBass) | [herakles.dev](https://herakles.dev) | hello@herakles.dev

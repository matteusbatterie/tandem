# Tandem

[![CI](https://github.com/matteusbatterie/tandem/actions/workflows/ci.yml/badge.svg)](https://github.com/matteusbatterie/tandem/actions/workflows/ci.yml)

Resilient two-way sync between Trello and GitHub Issues, built on .NET 10.

> **Status: work in progress.** The solution skeleton and CI are in place; core features are being built.

## Why

Keeping two tools in sync sounds simple until you meet duplicated webhooks, echo loops, conflicting edits and rate limits.
Tandem is a study in doing that reliably: idempotent processing, retries with backoff, and a clear conflict rule,
with connectors that can be swapped or extended.

## Planned scope

- Webhook ingestion that acknowledges fast and processes asynchronously
- Canonical item model with pluggable connectors (Trello, GitHub Issues first)
- Identity mapping between external items
- Idempotent processing, retries and a dead-letter path
- Echo-loop prevention and an explicit conflict-resolution rule
- Structured logging and OpenTelemetry traces

## Architecture

Clean Architecture, with dependencies pointing inward:

| Project | Responsibility |
|---|---|
| `Tandem.Domain` | Core model and sync rules, no dependencies |
| `Tandem.Application` | Use cases and ports (`IConnector`, ...) |
| `Tandem.Infrastructure` | Persistence, queue/outbox, resilience |
| `Tandem.Connectors.*` | One project per external system |
| `Tandem.Api` | Receives webhooks and stores raw events |
| `Tandem.Worker` | Processes queued events |

Design decisions are recorded in [`docs/adr`](docs/adr).

## Getting started

Requirements: [.NET 10 SDK](https://dotnet.microsoft.com/download) and Docker.

```bash
docker compose up -d        # PostgreSQL
dotnet build
dotnet test
```

Copy `.env.example` to `.env` to override the default local database credentials.

## License

To be defined.

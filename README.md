# LLM Gateway for .NET

A self-hosted LLM gateway for .NET: virtual API keys, per-team cost tracking,
rate limits, caching and model failover. Built with ASP.NET Core, YARP and .NET Aspire.

> **Status:** work in progress.

## Why
Apps that call LLM providers directly end up with scattered API keys, no cost
visibility, no limits and no failover. This gateway sits in between: apps change
only their base URL and API key, and get all of that from one place.

## Planned features
- Virtual API keys per team, with allowed models and monthly budgets
- Cost tracking per key and model, including streaming responses
- Rate limits (requests/minute) and token quotas (tokens/day)
- Exact-match response cache
- Fallback to a backup model on errors and timeouts
- Personal data redaction before prompts leave the network
- OpenTelemetry traces and metrics, Blazor dashboard

## Solution layout
| Project | Purpose |
|---|---|
| `src/LlmGateway.AppHost` | Aspire orchestration: Postgres, gateway, dashboard, mock provider |
| `src/LlmGateway.ServiceDefaults` | OpenTelemetry, health checks, resilience defaults |
| `src/LlmGateway.Core` | Provider adapters, SSE parsing, pricing, routing, redaction (no ASP.NET dependency) |
| `src/LlmGateway.Data` | EF Core DbContext, entities, migrations |
| `src/LlmGateway.Gateway` | The proxy: endpoints, middleware, YARP forwarding, admin API |
| `src/LlmGateway.Dashboard` | Blazor Server dashboard |
| `src/LlmGateway.MockProvider` | Fake Anthropic/OpenAI API for development and tests |
| `tools/LlmGateway.LoadGen` | Generates demo traffic |
| `tests/*` | Unit and integration tests |

## Tech stack
.NET 10 · ASP.NET Core · YARP · Aspire · PostgreSQL · EF Core · HybridCache ·
OpenTelemetry · Blazor · xUnit v3 · Testcontainers

## Quick start
Requires the .NET 10 SDK and Docker.

```bash
dotnet run --project src/LlmGateway.AppHost
```

## License
[MIT](LICENSE)

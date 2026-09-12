# Architecture: Sovereign HouseWisper CRM

## Overview

**Package ID:** `PKG-027`  
**Domain:** Real Estate Workflow Automation  
**Microservice Port:** `8807`  
**n8n Webhook Path:** `housewisper-crm-trigger`  
**GitHub:** [BlackFoxgamingstudio/housewisper-crm](https://github.com/BlackFoxgamingstudio/housewisper-crm)

AI-powered real estate CRM with lead nurturing, property valuation models, appointment scheduling, offer tracking, and market trend analysis.

---

## System Architecture

```
                     ┌──────────────────────────────────┐
                     │       Sovereign HouseWisper CRM     │
                     │       Port: 8807            │
                     ├──────────────┬───────────────────┤
   n8n Webhook ────▶ │  REST API    │   Core Engine     │
   HTTP POST         │  /api/v1/*   │   Dispatcher      │
                     └──────┬───────┴────────┬──────────┘
                            │                │
              ┌─────────────▼────────────────▼─────────┐
              │          Component Layer                 │
              │  LeadNurturer    | ValuationEngine | AppointmentS  │
              └────────────────────────┬────────────────┘
                                       │
              ┌────────────────────────▼────────────────┐
              │      n8n Central Event Bus (:5678)       │
              └─────────────────────────────────────────┘
```

## Core Components

### `LeadNurturer`
Handles all leadnurturer operations. Exposes async methods callable from the core dispatcher.

### `ValuationEngine`
Handles all valuation operations. Exposes async methods callable from the core dispatcher.

### `AppointmentScheduler`
Handles all appointmentscheduler operations. Exposes async methods callable from the core dispatcher.

### `OfferTracker`
Handles all offertracker operations. Exposes async methods callable from the core dispatcher.

### `MarketAnalyzer`
Handles all marketanalyzer operations. Exposes async methods callable from the core dispatcher.

---

## API Contract

All interactions follow the SBB standard envelope:

```http
POST /api/v1/execute
Content-Type: application/json
X-SBB-API-Key: <api-key>

{
  "action": "<operation>",
  "payload": {},
  "trace_id": "optional-uuid"
}
```

**Success Response (HTTP 200):**
```json
{
  "status": "success",
  "data": {},
  "trace_id": "...",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Health Check:**
```http
GET /health
→ {"status": "healthy", "service": "sovereign-housewisper-crm", "port": 8807}
```

## Integration Matrix

| System | Protocol | Direction | Purpose |
|--------|----------|-----------|---------|
| n8n Event Bus (:5678) | HTTP POST | Outbound | Event forwarding |
| n8n Webhook | HTTP POST | Inbound | Trigger execution |
| SBB Codebase Vault (:8766) | HTTP | Outbound | Code analysis |
| SBB Patterns Bible (:8794) | HTTP | Outbound | Standards validation |
| External APIs | HTTPS | Outbound | Domain-specific data |

## Deployment Architecture

```yaml
# docker-compose excerpt
sovereign-housewisper-crm:
  image: sovereign-housewisper-crm:latest
  ports: ["8807:8807"]
  healthcheck:
    test: curl -f http://localhost:8807/health
    interval: 30s
```

## Security Model

| Control | Implementation |
|---------|---------------|
| Authentication | `X-SBB-API-Key` header (env: `SBB_API_KEY`) |
| Rate Limiting | 100 req/min per client IP |
| Input Validation | Pydantic models (strict mode) |
| Container Security | Non-root user (`appuser:1001`) |
| Secrets | Environment variables only (never hardcoded) |
| TLS | Terminate at reverse proxy (nginx/caddy) |

## Tags
`crm`, `real-estate`, `ai`, `mls`

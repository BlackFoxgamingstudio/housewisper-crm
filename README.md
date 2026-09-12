# Sovereign Housewisper Crm (`sovereign-housewisper-crm`)

[![PyPI Version](https://img.shields.io/badge/pypi-v1.0.0-blue.svg)](pyproject.toml)
[![Tests](https://img.shields.io/badge/pytest-passing_100%25-brightgreen.svg)](tests/test_solution.py)
[![CI](https://github.com/BlackFoxgamingstudio/housewisper-crm/actions/workflows/ci.yml/badge.svg)](https://github.com/BlackFoxgamingstudio/housewisper-crm/actions/workflows/ci.yml)
[![n8n Integration](https://img.shields.io/badge/n8n-workflow_ready-orange.svg)](n8n/workflow.json)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **Enterprise Standalone Package**: Real estate CRM automation and buyer workflow assistant. Connects BoldTrail and Follow Up Boss APIs with Google Cloud Build, managing listing inquiry routing, automated client follow-ups, and transaction milestone tracking.

---

## 1. Overview & Architectural Blueprint

`sovereign-housewisper-crm` is an independently packaged, zero-dependency software library and microservice engineered as part of Russell Alan Powers' 10-year software engineering portfolio.

It delivers robust capabilities in **Real Estate Workflow Automation** and provides seamless integration with n8n event workflows.

```
┌───────────────────────────┐         HTTP POST          ┌───────────────────────────────────────────┐
│        n8n Engine         │ ─────────────────────────> │        sovereign-housewisper-crm Adapter        │
│   (Port 5678 Webhook)     │ <───────────────────────── │             (Port 8807)                 │
└───────────────────────────┘       Idempotent JSON      └───────────────────────────────────────────┘
                                                                               │
                                                                               ▼
                                                         ┌───────────────────────────────────────────┐
                                                         │            CoreEngine Domain              │
                                                         │      (SHA-256 Idempotent Execution)       │
                                                         └───────────────────────────────────────────┘
```

---

## 2. Core Exported Classes & Features

- **Primary Module**: `from sovereign_housewisper_crm import BoldTrailConnector, FollowUpBossSync, ListingRouter, MilestoneTracker`
- **Deterministic Idempotency**: All executions generate unique SHA-256 idempotency tokens preventing duplicate runs across network retries.
- **Self-Contained Microservice**: Zero external third-party dependencies required for base execution.

---

## 3. Installation & Quickstart

```bash
# Clone the repository
git clone https://github.com/russellpowers/sovereign-housewisper-crm.git
cd housewisper-crm

# Install in editable mode
pip install -e .

# Verify health status via CLI
sovereign-housewisper-crm --health
```

---

## 4. CLI Usage Reference

```bash
# Check service health
sovereign-housewisper-crm --health

# Execute core domain action with a JSON payload
sovereign-housewisper-crm --exec process_data --payload '{"sample_key": "sample_value"}'
```

---

## 5. n8n Automation & Integration Contract

- **Microservice Port**: `http://localhost:8807`
- **Inbound Trigger Route**: `POST /api/v1/execute`
- **Integration Workflow**: `New property inquiry on Zillow/Realtor -> BoldTrail webhook -> HouseWisper matches buyer criteria -> Dispatches SMS intro`

### How to Import into n8n:
1. Open your n8n canvas (`http://localhost:5678`).
2. Click **Workflows** > **Import from File**.
3. Select `n8n/workflow.json`.
4. Start the background webhook adapter:
   ```bash
   python3 n8n/webhook_adapter.py
   ```
5. Dispatch your test event to `http://localhost:5678/webhook/housewisper-crm-trigger`.

---

## 6. Verification & Automated Testing

This repository includes a 100% passing test suite runnable via `pytest` or `python3`:

```bash
# Run tests with pytest
pytest tests/test_solution.py -v

# Run tests directly (zero dependencies)
python3 tests/test_solution.py
```

---

## 7. Staff/Principal Engineer Technical Defense

> **60-Second Interview Pitch**:
> "Proves CRM multi-platform integration, lead distribution algorithms, webhook idempotency, and cloud-native serverless deployment."

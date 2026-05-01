# F1 Integrated Systems

A Predictive Maintenance & Race Strategy Optimization Platform inspired by the systems used by Formula 1 teams.

> **Status:** Work in progress — currently in planning and foundation phase.

## What This Is

A full-stack platform that simulates a race weekend environment where component health telemetry (engine, gearbox, brakes, tires) feeds into a predictive maintenance model. Those health predictions then act as dynamic constraints for a race strategy engine that recommends pit stop timing, tire selection, and pace management.

## Core Components

- **Telemetry Simulator** — generates realistic sensor streams with configurable degradation and failure modes
- **Predictive Maintenance Model** — anomaly detection and remaining useful life (RUL) prediction per component
- **Strategy Optimizer** — constraint-based race strategy engine driven by component health predictions
- **Real-time Dashboard** — live telemetry, component health gauges, and dynamic strategy timeline

## Tech Stack

| Layer | Technologies |
|---|---|
| Backend | Python 3.11, FastAPI, PostgreSQL, TimescaleDB |
| ML | scikit-learn, PyTorch |
| Strategy | OR-Tools |
| Real-time | Redis Streams, WebSockets |
| Frontend | React, Next.js, TailwindCSS |
| Infrastructure | Docker, GitHub Actions |

## Project Structure

```
f1-integrated-systems/
├── backend/          # FastAPI application
├── frontend/         # React / Next.js dashboard
├── ml-notebooks/     # Jupyter notebooks for model exploration
├── docs/             # MkDocs documentation
└── docker-compose.yml
```

## Getting Started

> Setup instructions will be added at the end of Phase 1.

## Roadmap

- [ ] Phase 1 — Foundation & Data Layer
- [ ] Phase 2 — Predictive Maintenance Model
- [ ] Phase 3 — Strategy Optimization Engine
- [ ] Phase 4 — Full Integration & Dashboard
- [ ] Phase 5 — Polish, Testing & Documentation

---

*Portfolio project targeting software engineering and AI roles in motorsport.*

# Software Requirements Specification
## F1 Integrated Systems — Predictive Maintenance & Race Strategy Optimization Platform

---

**Document Number:** F1IS-SRS-001  
**Version:** 1.0  
**Status:** Draft  
**Date:** May 2026  
**Prepared by:** F1 Integrated Systems Project  
**Standard:** ISO/IEC/IEEE 29148:2018

---

## Revision History

| Version | Date | Author | Description |
|---|---|---|---|
| 0.1 | May 2026 | Project Team | Initial draft |
| 1.0 | May 2026 | Project Team | First complete version |

---

## Table of Contents

1. [Introduction](#1-introduction)
   - 1.1 [Purpose](#11-purpose)
   - 1.2 [Scope](#12-scope)
   - 1.3 [Product Overview](#13-product-overview)
   - 1.4 [Definitions, Acronyms, and Abbreviations](#14-definitions-acronyms-and-abbreviations)
   - 1.5 [Document Conventions](#15-document-conventions)
   - 1.6 [Intended Audience and Reading Suggestions](#16-intended-audience-and-reading-suggestions)
   - 1.7 [References](#17-references)
2. [Overall Description](#2-overall-description)
   - 2.1 [Product Perspective](#21-product-perspective)
   - 2.2 [Product Functions](#22-product-functions)
   - 2.3 [User Classes and Characteristics](#23-user-classes-and-characteristics)
   - 2.4 [Operating Environment](#24-operating-environment)
   - 2.5 [Design and Implementation Constraints](#25-design-and-implementation-constraints)
   - 2.6 [Assumptions and Dependencies](#26-assumptions-and-dependencies)
3. [Specific Requirements](#3-specific-requirements)
4. [External Interface Requirements](#4-external-interface-requirements)
   - 4.1 [User Interfaces](#41-user-interfaces)
   - 4.2 [Hardware Interfaces](#42-hardware-interfaces)
   - 4.3 [Software Interfaces](#43-software-interfaces)
   - 4.4 [Communications Interfaces](#44-communications-interfaces)
5. [System Features](#5-system-features)
   - 5.1 [Telemetry Simulator](#51-telemetry-simulator)
   - 5.2 [Data Ingestion](#52-data-ingestion)
   - 5.3 [Predictive Maintenance Model](#53-predictive-maintenance-model)
   - 5.4 [Strategy Optimizer](#54-strategy-optimizer)
   - 5.5 [Backend API](#55-backend-api)
   - 5.6 [Race Engineer Dashboard View](#56-race-engineer-dashboard-view)
   - 5.7 [Strategist Dashboard View](#57-strategist-dashboard-view)
   - 5.8 [Team Principal Dashboard View](#58-team-principal-dashboard-view)
   - 5.9 [Historical Analysis](#59-historical-analysis)
6. [Other Nonfunctional Requirements](#6-other-nonfunctional-requirements)
   - 6.1 [Performance Requirements](#61-performance-requirements)
   - 6.2 [Safety Requirements](#62-safety-requirements)
   - 6.3 [Security Requirements](#63-security-requirements)
   - 6.4 [Software Quality Attributes](#64-software-quality-attributes)
   - 6.5 [Business Rules](#65-business-rules)
   - 6.6 [Compliance Requirements](#66-compliance-requirements)
7. [Other Requirements](#7-other-requirements)
- [Appendix A: Glossary](#appendix-a-glossary)
- [Appendix B: Analysis Models](#appendix-b-analysis-models)
- [Appendix C: TBD List](#appendix-c-tbd-list)
- [Appendix D: Requirements Traceability Matrix](#appendix-d-requirements-traceability-matrix)

---

## 1. Introduction

### 1.1 Purpose

This Software Requirements Specification (SRS) defines the functional and nonfunctional requirements for F1 Integrated Systems — a Predictive Maintenance and Race Strategy Optimization Platform. It specifies what the system shall do, the quality standards it shall meet, and the constraints it shall operate within.

This document serves as the contractual basis between stakeholders and the development team. All subsequent design, implementation, and testing work shall trace back to requirements defined here.

### 1.2 Scope

**Product name:** F1 Integrated Systems

**What the system does:**  
F1 Integrated Systems simulates a Formula 1 race weekend environment. It generates and streams car telemetry data, applies machine learning models to assess component health and predict failures, feeds those predictions into a race strategy optimizer, and presents the results in a role-specific real-time dashboard.

**What the system does not do:**  
- Connect to or receive data from real F1 car hardware
- Operate as a live race management tool for an actual race team
- Access or integrate with any proprietary F1 team systems

**Initial version scope:**  
v1.0 supports a single simulated car. Multi-car support is defined as a planned future extension and shall not require architectural changes to implement.

### 1.3 Product Overview

#### 1.3.1 Product Perspective

F1 Integrated Systems is a standalone application. It does not form part of a larger existing system. It sources real historical F1 session data via the FastF1 Python library solely for simulator calibration purposes, and generates synthetic telemetry data at runtime.

The system is architected as a layered pipeline:

```
┌─────────────────────────────────┐
│       Telemetry Simulator        │
└────────────────┬────────────────┘
                 ↓
┌─────────────────────────────────┐
│     Data Ingestion Layer         │
│  (Redis Streams + TimescaleDB)   │
└────────────────┬────────────────┘
                 ↓
┌─────────────────────────────────┐
│         ML / AI Engine           │
│  Anomaly Detection │ RUL Model   │
│      Strategy Optimizer          │
└────────────────┬────────────────┘
                 ↓
┌─────────────────────────────────┐
│          Backend API             │
│     (FastAPI + WebSocket)        │
└────────────────┬────────────────┘
                 ↓
┌─────────────────────────────────┐
│       Frontend Dashboard         │
│        (React + Next.js)         │
└─────────────────────────────────┘
```

#### 1.3.2 Product Functions

The system shall provide the following high-level functions:

- Generate and stream realistic car sensor telemetry during a simulated race
- Detect anomalies in sensor readings in real time
- Predict remaining useful life (RUL) for each monitored car component
- Optimize race strategy using component health predictions as dynamic constraints
- Present all data in real-time role-specific dashboard views
- Store simulation history for post-race analysis and replay

#### 1.3.3 User Characteristics

Three user roles interact with the system. Each has a distinct level of technical expertise and a distinct set of concerns. See Section 2.3 for full descriptions.

#### 1.3.4 Limitations

- The system relies on synthetic telemetry. Predictions are validated against simulated data only, not real car sensor streams.
- Strategy recommendations are optimizations within a simulated model. They are not validated against real race outcomes.
- The system is designed to run on a standard developer laptop and is not optimized for enterprise-scale deployment.

### 1.4 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|---|---|
| **ADR** | Architecture Decision Record — a short document capturing a significant design decision |
| **Anomaly** | A sensor reading that deviates significantly from the expected normal pattern for that component |
| **Compound** | Tire type: Soft, Medium, Hard, Intermediate, or Wet — each with distinct performance and degradation characteristics |
| **Component** | A physical subsystem of the car being monitored: engine, gearbox, brakes, or tires |
| **DRS** | Drag Reduction System — a moveable rear wing element on an F1 car used to reduce drag on specific track sections |
| **FastF1** | An open-source Python library providing access to official Formula 1 timing and telemetry data |
| **ML** | Machine Learning |
| **Overcut** | A race strategy in which a car stays out longer than a competitor before pitting, aiming to gain track position through tire age advantage |
| **Pit Window** | The recommended range of laps within which a pit stop should be made |
| **RUL** | Remaining Useful Life — the estimated number of laps or minutes before a component requires intervention |
| **SDD** | Software Design Document |
| **SRS** | Software Requirements Specification |
| **Stint** | A continuous period of racing on a single set of tires, between the start/a pit stop and the next pit stop/finish |
| **Telemetry** | Sensor data streamed from the car: tire temperature, brake wear, engine vibration, oil pressure, and fuel load |
| **TimescaleDB** | A time-series extension for PostgreSQL optimized for high-frequency sensor data |
| **Undercut** | A race strategy in which a car pits earlier than a competitor, fitting fresh tires to gain track position through superior lap times |
| **WebSocket** | A full-duplex communication protocol over a single TCP connection, used here for streaming live data to the frontend |

### 1.5 Document Conventions

**Requirement obligation keywords** (per RFC 2119 and ISO/IEC/IEEE 29148:2018):

| Keyword | Meaning |
|---|---|
| **shall** | Mandatory requirement — the system must satisfy this; it is verifiable and non-negotiable |
| **shall not** | Mandatory prohibition |
| **should** | Recommended but not mandatory; deviation must be justified |
| **may** | Optional — the system is permitted but not required to do this |
| **will** | Statement of fact about the environment, not a system requirement |

**Requirement ID format:**

```
[CATEGORY]-[SUBSYSTEM]-[NUMBER]
```

Examples:
- `FR-TS-001` — Functional Requirement, Telemetry Simulator, first requirement
- `NFR-PERF-001` — Nonfunctional Requirement, Performance, first requirement
- `IR-SW-001` — Interface Requirement, Software, first requirement

**Verification methods:**

| Code | Method |
|---|---|
| T | Test — verified by executing a defined test case |
| I | Inspection — verified by reviewing code, configuration, or documentation |
| D | Demonstration — verified by operating the system and observing behaviour |
| A | Analysis — verified by reviewing models, calculations, or design artefacts |

**Priority levels:**

| Level | Meaning |
|---|---|
| Critical | System cannot function without this; must be in v1.0 |
| High | Core feature; must be in v1.0 |
| Medium | Important but deferrable to a later iteration |
| Low | Nice to have; included if time permits |

### 1.6 Intended Audience and Reading Suggestions

| Audience | Recommended Sections |
|---|---|
| **Developer / Claude Code** | All sections, with emphasis on Sections 4, 5, and 6 |
| **Project Owner** | Sections 1, 2, and 5 (feature descriptions only) |
| **Tester** | Sections 5, 6, and Appendix D |
| **Reviewer / Employer** | Sections 1, 2, 5, and Appendix D |

### 1.7 References

| Reference | Description |
|---|---|
| F1IS-PLAN-001 | F1 Integrated Systems Project Plan v1.0 (`F1_Integrated_Systems_Project_Plan.pdf`) |
| ISO/IEC/IEEE 29148:2018 | Systems and software engineering — Life cycle processes — Requirements engineering |
| IEEE 830-1998 | IEEE Recommended Practice for Software Requirements Specifications (superseded) |
| RFC 2119 | Key words for use in RFCs to Indicate Requirement Levels |
| FastF1 Documentation | https://docs.fastf1.dev/ |
| Jolpica F1 API | https://api.jolpi.ca/ |

---

## 2. Overall Description

### 2.1 Product Perspective

F1 Integrated Systems is a new, standalone system with no predecessor. It does not replace an existing product. It is developed as a portfolio project to demonstrate applied software engineering and AI/ML skills in a motorsport context.

External data dependency: the FastF1 library is used once during development to fetch historical session data for calibrating the telemetry simulator's degradation curves. At runtime, the system is self-contained and does not require internet access.

### 2.2 Product Functions

| # | Function | Description |
|---|---|---|
| F-01 | Telemetry Generation | Simulate and stream sensor readings for all monitored car components |
| F-02 | Data Persistence | Store all telemetry in a time-series database for querying and replay |
| F-03 | Anomaly Detection | Identify abnormal sensor patterns in real time and generate alerts |
| F-04 | RUL Prediction | Forecast how many laps each component can continue before requiring intervention |
| F-05 | Strategy Optimization | Produce pit stop, tire compound, and pace recommendations using health constraints |
| F-06 | Live Dashboard | Display telemetry, health, and strategy data in real time across three role-specific views |
| F-07 | Historical Analysis | Store and replay completed simulation sessions; compare strategy outcomes |

### 2.3 User Classes and Characteristics

#### Race Engineer
- **Role:** Monitors the car's technical condition during the race
- **Technical expertise:** High — understands sensor data, degradation curves, and component behaviour
- **Interaction frequency:** Continuous throughout a simulation session
- **Primary concerns:** Raw sensor readings, anomaly flags, RUL estimates, component degradation trends
- **Privileges:** Read access to all telemetry data; receives all alert levels

#### Strategist
- **Role:** Makes race strategy decisions — when to pit, which tire compound to use, what pace to run
- **Technical expertise:** Medium — understands race dynamics, tire behaviour, and competitive positioning; does not require raw sensor data
- **Interaction frequency:** Continuous throughout a simulation session
- **Primary concerns:** Pit window recommendations, tire compound selection, pace targets, competitor gaps, undercut/overcut opportunities
- **Privileges:** Read access to strategy data and component health summaries; receives strategy-relevant alerts

#### Team Principal
- **Role:** High-level race oversight and final decision authority
- **Technical expertise:** Low to medium — focuses on race outcome and key decisions, not technical detail
- **Interaction frequency:** Periodic — checks in at key moments rather than continuously
- **Primary concerns:** Race position, overall car health summary, active strategy recommendation, critical alerts only
- **Privileges:** Read access to summary-level data only; receives critical alerts only

### 2.4 Operating Environment

| Environment | Specification |
|---|---|
| **Local development** | Docker Desktop on macOS or Linux; 8GB RAM minimum; 20GB free disk space |
| **Browser support** | Google Chrome 120+, Mozilla Firefox 120+, Safari 17+ |
| **Backend runtime** | Python 3.11+, containerised via Docker |
| **Frontend runtime** | Node.js 18+, containerised via Docker |
| **Database** | PostgreSQL 15 with TimescaleDB 2.x extension |
| **Cache / stream** | Redis 7.x |
| **Cloud deployment** | Single-instance deployment on Fly.io or Railway (free tier) |

### 2.5 Design and Implementation Constraints

| ID | Constraint |
|---|---|
| C-01 | The backend shall be implemented in Python 3.11 or later |
| C-02 | The frontend shall be implemented in TypeScript using React 18 and Next.js |
| C-03 | All services shall be containerised using Docker and orchestrated via Docker Compose |
| C-04 | The API shall be implemented using FastAPI and shall expose an OpenAPI specification |
| C-05 | Time-series telemetry data shall be stored in TimescaleDB |
| C-06 | The strategy optimizer shall use OR-Tools as its primary constraint solver |
| C-07 | The system shall not connect to any live F1 team data systems |
| C-08 | The system shall be startable with a single `docker-compose up` command |
| C-09 | The CI/CD pipeline shall use GitHub Actions |

### 2.6 Assumptions and Dependencies

| ID | Type | Statement |
|---|---|---|
| A-01 | Assumption | The FastF1 Python library remains available and able to fetch historical session data for simulator calibration |
| A-02 | Assumption | The developer machine has Docker Desktop installed and running |
| A-03 | Assumption | Python 3.11+ and Node.js 18+ are available in the development environment |
| A-04 | Assumption | A stable internet connection is available during initial data fetch from FastF1 (not required at runtime) |
| A-05 | Dependency | OR-Tools Python package is installable via pip |
| A-06 | Dependency | Stable Baselines3 is installable via pip (for future RL extension) |
| A-07 | Dependency | TimescaleDB Docker image is available from Docker Hub |

---

## 3. Specific Requirements

All specific requirements are defined in Sections 4, 5, and 6 of this document, following the ISO/IEC/IEEE 29148:2018 structure.

- **External interface requirements** → Section 4
- **Functional requirements (by system feature)** → Section 5
- **Nonfunctional requirements** → Section 6

---

## 4. External Interface Requirements

### 4.1 User Interfaces

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| IR-UI-001 | The system shall provide a web-based dashboard accessible via a standard browser without installing any client software | Critical | D |
| IR-UI-002 | The dashboard shall provide three distinct views selectable by the user: Race Engineer, Strategist, and Team Principal | High | D |
| IR-UI-003 | Each view shall use colour-coded component health indicators: green (healthy: anomaly score < 0.3), amber (degrading: 0.3 ≤ score < 0.7), red (critical: score ≥ 0.7) | High | T |
| IR-UI-004 | The dashboard shall display a live lap counter and race timer visible in all views | High | D |
| IR-UI-005 | All alert notifications shall be displayed with a timestamp accurate to the nearest second of simulation time | Medium | T |
| IR-UI-006 | The dashboard shall be usable on a minimum screen width of 1280px | Medium | I |

### 4.2 Hardware Interfaces

The system does not interface with any physical hardware. All sensor data is generated programmatically by the telemetry simulator.

### 4.3 Software Interfaces

| ID | External System | Interface Type | Data Exchanged | Requirement |
|---|---|---|---|---|
| IR-SW-001 | FastF1 (Python library) | Python API call | Historical lap times, tire stint data, sector times | The system shall use FastF1 to fetch calibration data for at least two historical race sessions during simulator setup | 
| IR-SW-002 | PostgreSQL / TimescaleDB | SQL over TCP | Telemetry records, session metadata, strategy history | The system shall persist all telemetry readings to TimescaleDB within 100ms of generation |
| IR-SW-003 | Redis | Redis Streams protocol | Raw telemetry messages | The system shall publish each telemetry reading to a Redis Stream immediately upon generation |
| IR-SW-004 | OR-Tools (Python library) | Python API call | Strategy constraints, optimization result | The strategy optimizer shall use OR-Tools CP-SAT solver for all constraint-based strategy calculations |
| IR-SW-005 | Jolpica F1 API | REST / HTTPS | Historical race results, standings | The system may use the Jolpica API as a secondary data source for strategy model validation |

### 4.4 Communications Interfaces

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| IR-COM-001 | The backend API shall expose RESTful endpoints over HTTP/HTTPS on a configurable port (default: 8000) | Critical | T |
| IR-COM-002 | The backend shall provide a WebSocket endpoint for streaming live telemetry, health updates, and strategy changes to connected frontend clients | Critical | T |
| IR-COM-003 | All REST API requests and responses shall use JSON as the data exchange format | Critical | I |
| IR-COM-004 | WebSocket messages shall use JSON-encoded payloads with a defined message type field | High | I |
| IR-COM-005 | The frontend shall attempt to reconnect to the WebSocket server automatically if the connection is dropped, with exponential backoff up to a maximum of 30 seconds between retries | High | T |

---

## 5. System Features

Each feature is described with: a description, the stimulus/response sequence that activates it, and its individual functional requirements.

---

### 5.1 Telemetry Simulator

#### 5.1.1 Description
The telemetry simulator is the data source for the entire system. It generates synthetic sensor readings that model realistic F1 car behaviour over a race distance, including component degradation and configurable failure injection.

**Priority:** Critical

#### 5.1.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| User starts a simulation session via the API | Simulator begins generating sensor readings at the configured interval |
| User pauses the session | Simulator halts data generation; state is preserved |
| User resumes the session | Simulator continues from the preserved state |
| User stops the session | Simulator halts; session data is finalised and stored |
| Failure injection is configured for a component at lap N | When simulation reaches lap N, the affected component's sensor readings degrade according to the configured failure mode |

#### 5.1.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-TS-001 | The simulator shall generate sensor readings for the following channels: tire temperature (four values, one per wheel), brake wear (four values, one per corner), engine vibration (one value), oil pressure (one value), and fuel load (one value) | Critical | T |
| FR-TS-002 | The simulator shall generate sensor readings at a configurable interval with a default of 500 milliseconds | Critical | T |
| FR-TS-003 | The simulator shall model a degradation curve for each component that progresses over the race distance in a manner consistent with real F1 behaviour | High | A |
| FR-TS-004 | The simulator shall support configurable failure injection: a user shall be able to specify a component and a lap number at which that component enters a degraded or failure state | High | T |
| FR-TS-005 | Degradation curves shall be calibrated against at least two real historical F1 race sessions fetched via the FastF1 library | High | I |
| FR-TS-006 | The simulator shall model decreasing fuel load over the race distance and shall apply the corresponding lap time delta (approximately 0.03 seconds per kilogram of fuel) | Medium | A |
| FR-TS-007 | The simulator shall produce a lap time estimate each lap based on: current tire compound age, fuel load, and aggregate component health score | Medium | T |
| FR-TS-008 | The simulator shall add configurable Gaussian noise to all sensor readings to prevent artificially clean data | Medium | I |

---

### 5.2 Data Ingestion

#### 5.2.1 Description
The data ingestion layer receives telemetry from the simulator, routes it to the real-time stream for immediate consumption by the ML engine, and persists it to the time-series database for historical analysis.

**Priority:** Critical

#### 5.2.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| Simulator emits a telemetry reading | System publishes it to the Redis Stream and writes it to TimescaleDB |
| A consumer requests historical telemetry | System queries TimescaleDB and returns the result set |

#### 5.2.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-DI-001 | The system shall publish every telemetry reading to a Redis Stream within 50 milliseconds of generation | Critical | T |
| FR-DI-002 | The system shall persist every telemetry reading to TimescaleDB with a timestamp accurate to the millisecond | Critical | T |
| FR-DI-003 | The system shall retain the complete telemetry history for all simulation sessions | High | T |
| FR-DI-004 | The system shall support querying historical telemetry filtered by: component, sensor channel, time range, and session ID | High | T |
| FR-DI-005 | No telemetry reading shall be discarded between the simulator and TimescaleDB under normal operating conditions | Critical | T |

---

### 5.3 Predictive Maintenance Model

#### 5.3.1 Description
The predictive maintenance model consumes the telemetry stream and runs two models: an anomaly detector that scores each component's current state, and an RUL predictor that forecasts how many laps remain before each component requires attention. The outputs drive both the alert system and the strategy optimizer.

**Priority:** Critical

#### 5.3.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| Inference cycle triggers (every 2–5 seconds of simulation time) | System reads the latest telemetry window from Redis, runs both models, and publishes results to the API and alert pipeline |
| Component anomaly score exceeds the alert threshold | System generates an alert event with the component ID, score, and simulation timestamp |
| Component RUL drops below the configured warning threshold | System generates a RUL warning event |
| Component RUL reaches zero | System generates a critical failure-imminent alert |

#### 5.3.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-PM-001 | The anomaly detection model shall compute an anomaly score between 0.0 and 1.0 for each monitored component on every inference cycle | Critical | T |
| FR-PM-002 | The anomaly detection model shall use a rolling window of the last N sensor readings as its input, where N is configurable | High | I |
| FR-PM-003 | The system shall flag a component as anomalous when its score equals or exceeds a configurable threshold (default: 0.7) | Critical | T |
| FR-PM-004 | The RUL prediction model shall output an estimated number of laps remaining for each component | Critical | T |
| FR-PM-005 | The inference cycle shall run at a configurable interval with a default of 3 seconds of simulation time | High | T |
| FR-PM-006 | The system shall expose current anomaly scores and RUL predictions for all components via the backend API | Critical | T |
| FR-PM-007 | When a component's RUL drops below a configurable warning threshold (default: 10 laps), the system shall generate a RUL warning alert | High | T |
| FR-PM-008 | Anomaly detection shall be implemented using an Isolation Forest model | High | I |
| FR-PM-009 | RUL prediction shall be implemented using an LSTM neural network with a temporal sliding window | High | I |

---

### 5.4 Strategy Optimizer

#### 5.4.1 Description
The strategy optimizer uses the component health predictions as hard constraints and solves for the race plan that minimizes total race time while complying with all regulatory and physical constraints. It recalculates whenever the health picture changes.

**Priority:** Critical

#### 5.4.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| New component health prediction is received | Optimizer recalculates the race plan using updated health constraints |
| Simulation reaches a new lap | Optimizer updates race state (position, gaps, tire age) and re-evaluates the current plan |
| Component is flagged critical (RUL ≤ 5 laps or anomaly score ≥ 0.9) | Optimizer immediately recommends an emergency pit stop at the earliest opportunity |
| User requests A/B strategy comparison | Optimizer generates two strategy variants and returns both |

#### 5.4.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-SO-001 | The optimizer shall produce a recommended race plan specifying: planned pit lap(s), tire compound per stint, and target lap time per stint | Critical | T |
| FR-SO-002 | Component RUL predictions shall be used as hard upper-bound constraints on stint length | Critical | T |
| FR-SO-003 | The optimizer shall enforce the FIA mandatory compound rule: at least two different dry tire compounds shall be used in a race | Critical | T |
| FR-SO-004 | The optimizer shall account for a configurable pit lane time loss (default: 22 seconds) in its total race time calculation | High | T |
| FR-SO-005 | The optimizer shall recalculate the race plan within 5 seconds of receiving a component health update | High | T |
| FR-SO-006 | When the simulated gap to the car ahead is within a configurable threshold (default: 2 seconds), the optimizer shall evaluate and flag an undercut opportunity | Medium | T |
| FR-SO-007 | When the simulated gap to the car behind is within a configurable threshold (default: 3 seconds), the optimizer shall evaluate and flag an overcut opportunity | Medium | T |
| FR-SO-008 | The optimizer shall store a timestamped history of all strategy recommendations made during a session | High | T |
| FR-SO-009 | The optimizer shall be implemented using OR-Tools CP-SAT solver as its primary computation engine | Critical | I |
| FR-SO-010 | The optimizer shall support returning two strategy variants simultaneously for A/B comparison | Medium | T |

---

### 5.5 Backend API

#### 5.5.1 Description
The backend API is the single interface through which the frontend, and any external consumer, communicates with the system. It exposes RESTful endpoints for data retrieval and session management, and a WebSocket connection for live streaming.

**Priority:** Critical

#### 5.5.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| Client sends POST /sessions/start | API starts a new simulation session and returns a session ID |
| Client sends POST /sessions/{id}/pause | API pauses the active session |
| Client sends POST /sessions/{id}/stop | API stops the session and finalises storage |
| Client connects to WebSocket /ws/{session_id} | API begins streaming telemetry, health updates, and strategy events to the client |
| Client sends invalid request payload | API returns a structured 422 error with field-level validation details |

#### 5.5.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-API-001 | The API shall provide endpoints for session lifecycle management: create, start, pause, resume, and stop | Critical | T |
| FR-API-002 | The API shall provide endpoints to query current and historical telemetry data | High | T |
| FR-API-003 | The API shall provide endpoints to query current component health status (anomaly scores and RUL) | Critical | T |
| FR-API-004 | The API shall provide endpoints to retrieve the current and historical strategy recommendations | Critical | T |
| FR-API-005 | The API shall provide a WebSocket endpoint that streams telemetry readings, health updates, and strategy change events in real time | Critical | T |
| FR-API-006 | All API request and response schemas shall be validated using Pydantic models | High | I |
| FR-API-007 | The API shall return structured JSON error responses for all 4xx and 5xx status codes, including an error code, message, and request ID | High | T |
| FR-API-008 | The API shall automatically generate and serve an OpenAPI 3.0 specification at /docs | High | D |

---

### 5.6 Race Engineer Dashboard View

#### 5.6.1 Description
The Race Engineer view provides full technical visibility into the car's sensor data and component health. It is the highest-detail view in the system.

**Priority:** High

#### 5.6.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| WebSocket delivers a new telemetry batch | Telemetry strip charts update in real time |
| Component anomaly score crosses a threshold | Health gauge changes colour; alert appears in the feed |
| User hovers over a data point on a chart | Tooltip displays the exact reading, timestamp, and component |

#### 5.6.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-RE-001 | The view shall display a live line chart for each sensor channel, updating at 500-millisecond intervals via WebSocket | High | D |
| FR-RE-002 | The view shall display a health gauge per component showing the current anomaly score and RUL estimate | Critical | D |
| FR-RE-003 | Health gauges shall update their colour state (green / amber / red) within 1 second of a threshold crossing | High | T |
| FR-RE-004 | The view shall display a scrolling alert feed showing all anomaly events with simulation timestamps, in descending chronological order | High | D |
| FR-RE-005 | The view shall display the degradation trend for each component over the current stint as a time-series chart | Medium | D |

---

### 5.7 Strategist Dashboard View

#### 5.7.1 Description
The Strategist view focuses on race strategy data. It shows pit window recommendations, tire choices, pace targets, competitive context, and allows direct comparison of two strategy options.

**Priority:** High

#### 5.7.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| Optimizer produces a new strategy recommendation | Strategy timeline updates dynamically; changed pit windows are visually highlighted |
| Undercut or overcut opportunity is flagged | The view displays a highlighted opportunity card with the relevant lap window and estimated time gain |
| User selects A/B comparison mode | View splits to show two strategy timelines side by side |

#### 5.7.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-ST-001 | The view shall display a strategy timeline (Gantt-style) showing planned stints, pit lap windows, and tire compound per stint | Critical | D |
| FR-ST-002 | The strategy timeline shall visually update within 2 seconds of a strategy recalculation | High | T |
| FR-ST-003 | The view shall display the simulated gap (in seconds) to the car immediately ahead and the car immediately behind, updated each lap | High | D |
| FR-ST-004 | The view shall display the recommended pace target (lap time delta from optimal) for the current stint | High | D |
| FR-ST-005 | When an undercut or overcut opportunity is active, the view shall display a highlighted opportunity indicator with the recommended action lap and estimated position gain | Medium | D |
| FR-ST-006 | The view shall support an A/B strategy comparison mode that displays two strategy plans side by side with a projected race time delta | Medium | D |

---

### 5.8 Team Principal Dashboard View

#### 5.8.1 Description
The Team Principal view provides a high-level, low-noise summary of race status. It omits raw sensor data and presents only the information relevant to strategic decision-making at the leadership level.

**Priority:** Medium

#### 5.8.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| Race position changes | Position indicator updates immediately |
| A critical alert is generated by any subsystem | Alert appears in the Team Principal feed within 2 seconds |
| Strategy changes significantly (pit lap shifts by more than 2 laps) | View highlights the strategy change with the previous and updated recommendation |

#### 5.8.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-TP-001 | The view shall display the current race position, current lap number, and gap in seconds to the nearest competitor ahead and behind | High | D |
| FR-TP-002 | The view shall display a single aggregate health indicator per component (healthy / degrading / critical) with no raw sensor data | High | D |
| FR-TP-003 | The view shall display the active strategy recommendation in plain language (e.g., "Pit on lap 28 for Medium tyres") | High | D |
| FR-TP-004 | The view shall display only critical-severity alerts (anomaly score ≥ 0.9 or RUL ≤ 5 laps), not all anomaly events | High | T |
| FR-TP-005 | The view shall display a race delta: the difference in seconds between the current projected finish time and the optimal projected finish time | Medium | T |

---

### 5.9 Historical Analysis

#### 5.9.1 Description
The historical analysis feature allows users to review completed simulation sessions, replay them lap by lap, and compare the outcomes of different strategies.

**Priority:** Medium

#### 5.9.2 Stimulus / Response Sequences

| Stimulus | System Response |
|---|---|
| User selects a completed session for replay | System loads the session and renders the state at lap 1 |
| User advances to a specific lap during replay | System renders all telemetry, health, and strategy data for that lap |
| User selects two sessions for comparison | System renders a side-by-side comparison of strategy outcomes |

#### 5.9.3 Functional Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| FR-HA-001 | The system shall persist all data for a simulation session (telemetry, health predictions, strategy history) upon session completion | High | T |
| FR-HA-002 | The system shall provide a session list view showing all completed sessions with their date, race name, and final lap count | Medium | D |
| FR-HA-003 | Users shall be able to replay a completed session and view the state of all dashboard panels at any selected lap | Medium | D |
| FR-HA-004 | Users shall be able to select two completed sessions and view a comparison of their strategy timelines and projected finish times | Low | D |

---

## 6. Other Nonfunctional Requirements

### 6.1 Performance Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-PERF-001 | Telemetry data shall appear on the dashboard within 1 second of being generated by the simulator under normal operating conditions | Critical | T |
| NFR-PERF-002 | Strategy recalculation shall complete and the result shall be available via the API within 5 seconds of a health update being received | High | T |
| NFR-PERF-003 | The system shall sustain a telemetry generation rate of one batch per 500 milliseconds for the full duration of a simulated race (minimum 60 laps) without throughput degradation | Critical | T |
| NFR-PERF-004 | The backend API shall respond to 95% of REST requests within 500 milliseconds under a load of up to 5 concurrent clients | High | T |
| NFR-PERF-005 | The frontend dashboard shall maintain a frame rate of at least 30 FPS during live WebSocket data streaming | Medium | D |

### 6.2 Safety Requirements

The system does not control physical hardware and presents no physical safety risk. The following requirements address simulation integrity:

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-SAF-001 | The system shall not present a strategy recommendation that violates the FIA mandatory compound rule as a valid option | Critical | T |
| NFR-SAF-002 | The system shall clearly mark all displayed data as simulated and shall not imply that outputs represent real-world conditions | High | I |

### 6.3 Security Requirements

| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-SEC-001 | The system shall not expose database credentials, API keys, or secrets in the source code repository | Critical | I |
| NFR-SEC-002 | All environment-specific configuration (database URLs, secret keys) shall be managed via environment variables loaded from a `.env` file excluded from version control | Critical | I |
| NFR-SEC-003 | The cloud-deployed instance shall serve all traffic over HTTPS | High | T |

### 6.4 Software Quality Attributes

#### 6.4.1 Availability
| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-AVL-001 | The system shall handle a dropped WebSocket connection and the frontend shall automatically attempt reconnection with exponential backoff | High | T |
| NFR-AVL-002 | If the ML inference service becomes unavailable, the simulator and API shall continue operating and shall return the last known health values | High | T |

#### 6.4.2 Maintainability
| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-MNT-001 | Backend unit and integration test coverage shall be at least 80% of all non-trivial code paths | High | T |
| NFR-MNT-002 | All public API endpoints shall have at least one integration test | High | T |
| NFR-MNT-003 | The codebase shall pass ruff linting and mypy type checking with zero errors on every CI run | High | T |
| NFR-MNT-004 | The frontend shall pass ESLint with the project's configured ruleset on every CI run | Medium | T |

#### 6.4.3 Portability
| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-PRT-001 | The system shall run identically on macOS and Linux via Docker Compose | High | D |

#### 6.4.4 Reliability
| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-REL-001 | No telemetry reading shall be lost between the simulator and TimescaleDB under normal operating conditions | Critical | T |
| NFR-REL-002 | The system shall complete a full 60-lap simulation without crashing or requiring manual intervention | High | D |

#### 6.4.5 Scalability
| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-SCL-001 | The TimescaleDB schema shall be designed to support multiple concurrent car data streams without structural changes | High | A |
| NFR-SCL-002 | The strategy optimizer's interface shall accept a list of car states as input, enabling multi-car support to be added without redesigning the optimizer's core logic | Medium | I |

#### 6.4.6 Usability
| ID | Requirement | Priority | Verification |
|---|---|---|---|
| NFR-USE-001 | A user familiar with Formula 1 shall be able to interpret any dashboard view without consulting a user manual | High | D |
| NFR-USE-002 | The system shall be startable from a clean clone using a single command: `docker-compose up` | Critical | D |
| NFR-USE-003 | The README shall include a quickstart guide that brings a new user to a running simulation in under 10 minutes | High | I |

### 6.5 Business Rules

| ID | Rule | Verification |
|---|---|---|
| BR-001 | A simulation session shall not allow two pit stops on the same lap | T |
| BR-002 | Tire compounds classified as Intermediate or Wet shall only be available when a wet weather condition is active in the simulation | T |
| BR-003 | A component flagged as critical (anomaly score ≥ 0.9) shall always result in a strategy recommendation to pit within 3 laps | T |
| BR-004 | Strategy recommendations shall always respect the minimum stint length of 1 lap (a car cannot pit on consecutive laps) | T |

### 6.6 Compliance Requirements

| ID | Requirement | Verification |
|---|---|---|
| NFR-CMP-001 | The system shall comply with all open-source license terms for all third-party libraries used | I |
| NFR-CMP-002 | The system shall not store or transmit any personally identifiable information | I |

---

## 7. Other Requirements

No additional requirements outside the categories above are currently identified. This section will be updated if regulatory, data retention, or localisation requirements are identified during development.

---

## Appendix A: Glossary

See Section 1.4 — Definitions, Acronyms, and Abbreviations.

---

## Appendix B: Analysis Models

Analysis models (data flow diagrams, entity-relationship diagrams, state machines) will be produced as part of the Software Design Document (F1IS-SDD-001) and linked here upon completion.

---

## Appendix C: TBD List

| ID | Item | Owner | Target Resolution |
|---|---|---|---|
| TBD-001 | Exact number of laps for reference races to be used for FastF1 calibration | Project Team | Before Phase 1 start |
| TBD-002 | Configurable noise model parameters (mean, standard deviation per sensor) | Project Team | Phase 1 |
| TBD-003 | Exact pit lane time loss default value (currently set to 22s — to be validated against FastF1 data) | Project Team | Phase 3 |
| TBD-004 | Cloud deployment provider (Fly.io vs Railway) | Project Team | Phase 5 |

---

## Appendix D: Requirements Traceability Matrix

This matrix maps each requirement to its design section (to be completed in the SDD) and its test case (to be completed in the Test Plan).

| Requirement ID | Description (summary) | SDD Section | Test Case ID | Status |
|---|---|---|---|---|
| FR-TS-001 | Simulator generates all sensor channels | TBD | TBD | Draft |
| FR-TS-002 | Configurable 500ms generation interval | TBD | TBD | Draft |
| FR-TS-003 | Realistic degradation curves | TBD | TBD | Draft |
| FR-TS-004 | Failure injection by component and lap | TBD | TBD | Draft |
| FR-TS-005 | FastF1 calibration | TBD | TBD | Draft |
| FR-TS-006 | Fuel load model | TBD | TBD | Draft |
| FR-TS-007 | Lap time estimate | TBD | TBD | Draft |
| FR-TS-008 | Gaussian noise on sensor readings | TBD | TBD | Draft |
| FR-DI-001 | Publish to Redis Stream within 50ms | TBD | TBD | Draft |
| FR-DI-002 | Persist to TimescaleDB with ms timestamp | TBD | TBD | Draft |
| FR-DI-003 | Retain full session history | TBD | TBD | Draft |
| FR-DI-004 | Query historical telemetry by filters | TBD | TBD | Draft |
| FR-DI-005 | No telemetry lost in transit | TBD | TBD | Draft |
| FR-PM-001 | Anomaly score 0.0–1.0 per component | TBD | TBD | Draft |
| FR-PM-002 | Rolling window input | TBD | TBD | Draft |
| FR-PM-003 | Flag anomaly at configurable threshold | TBD | TBD | Draft |
| FR-PM-004 | RUL output in laps | TBD | TBD | Draft |
| FR-PM-005 | Configurable inference cycle | TBD | TBD | Draft |
| FR-PM-006 | Expose scores and RUL via API | TBD | TBD | Draft |
| FR-PM-007 | RUL warning alert at threshold | TBD | TBD | Draft |
| FR-PM-008 | Isolation Forest for anomaly detection | TBD | TBD | Draft |
| FR-PM-009 | LSTM for RUL prediction | TBD | TBD | Draft |
| FR-SO-001 | Race plan: pit laps, compounds, pace | TBD | TBD | Draft |
| FR-SO-002 | RUL as hard stint-length constraint | TBD | TBD | Draft |
| FR-SO-003 | Mandatory compound rule enforced | TBD | TBD | Draft |
| FR-SO-004 | Pit lane time loss accounted for | TBD | TBD | Draft |
| FR-SO-005 | Recalculate within 5 seconds | TBD | TBD | Draft |
| FR-SO-006 | Undercut opportunity detection | TBD | TBD | Draft |
| FR-SO-007 | Overcut opportunity detection | TBD | TBD | Draft |
| FR-SO-008 | Strategy recommendation history | TBD | TBD | Draft |
| FR-SO-009 | OR-Tools CP-SAT solver | TBD | TBD | Draft |
| FR-SO-010 | A/B strategy variant output | TBD | TBD | Draft |
| FR-API-001 | Session lifecycle endpoints | TBD | TBD | Draft |
| FR-API-002 | Telemetry query endpoints | TBD | TBD | Draft |
| FR-API-003 | Health status endpoints | TBD | TBD | Draft |
| FR-API-004 | Strategy endpoints | TBD | TBD | Draft |
| FR-API-005 | WebSocket streaming endpoint | TBD | TBD | Draft |
| FR-API-006 | Pydantic validation | TBD | TBD | Draft |
| FR-API-007 | Structured error responses | TBD | TBD | Draft |
| FR-API-008 | OpenAPI spec at /docs | TBD | TBD | Draft |
| FR-RE-001 | Live telemetry strip charts at 500ms | TBD | TBD | Draft |
| FR-RE-002 | Health gauge per component | TBD | TBD | Draft |
| FR-RE-003 | Colour state update within 1 second | TBD | TBD | Draft |
| FR-RE-004 | Scrolling alert feed | TBD | TBD | Draft |
| FR-RE-005 | Degradation trend chart | TBD | TBD | Draft |
| FR-ST-001 | Strategy timeline (Gantt-style) | TBD | TBD | Draft |
| FR-ST-002 | Timeline updates within 2 seconds | TBD | TBD | Draft |
| FR-ST-003 | Gap to cars ahead/behind | TBD | TBD | Draft |
| FR-ST-004 | Pace target display | TBD | TBD | Draft |
| FR-ST-005 | Undercut/overcut opportunity indicator | TBD | TBD | Draft |
| FR-ST-006 | A/B comparison mode | TBD | TBD | Draft |
| FR-TP-001 | Race position, lap, gap display | TBD | TBD | Draft |
| FR-TP-002 | Aggregate health indicator per component | TBD | TBD | Draft |
| FR-TP-003 | Plain-language strategy display | TBD | TBD | Draft |
| FR-TP-004 | Critical alerts only | TBD | TBD | Draft |
| FR-TP-005 | Race delta display | TBD | TBD | Draft |
| FR-HA-001 | Persist completed session data | TBD | TBD | Draft |
| FR-HA-002 | Session list view | TBD | TBD | Draft |
| FR-HA-003 | Session replay by lap | TBD | TBD | Draft |
| FR-HA-004 | Two-session strategy comparison | TBD | TBD | Draft |
| NFR-PERF-001 | Telemetry on dashboard within 1s | TBD | TBD | Draft |
| NFR-PERF-002 | Strategy recalculation within 5s | TBD | TBD | Draft |
| NFR-PERF-003 | 500ms throughput sustained for 60 laps | TBD | TBD | Draft |
| NFR-PERF-004 | API 95th percentile response < 500ms | TBD | TBD | Draft |
| NFR-SEC-001 | No secrets in repository | TBD | TBD | Draft |
| NFR-SEC-002 | Environment variables for config | TBD | TBD | Draft |
| NFR-MNT-001 | 80% backend test coverage | TBD | TBD | Draft |
| NFR-USE-002 | Single command startup | TBD | TBD | Draft |

---

*Next document: Software Design Document (F1IS-SDD-001)*  
*See also: `DOCUMENTATION_PLAN.md`, `F1_Integrated_Systems_Project_Plan.pdf`*

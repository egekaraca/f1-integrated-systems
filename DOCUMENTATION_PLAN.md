# Documentation Plan — F1 Integrated Systems

Full software development process documentation roadmap.
The goal is a complete, professional package that demonstrates how software is built in industry.

---

## Documents to Produce

| Document | What It Is | When |
|---|---|---|
| **SRS** (Software Requirements Specification) | What the system must do — functional and non-functional requirements | Before any code |
| **SDD** (Software Design Document) | How the system will be built — architecture, components, data models, APIs | After SRS, before coding |
| **ADRs** (Architecture Decision Records) | Short records of *why* specific technical choices were made | As decisions are made during development |
| **API Reference** | Every endpoint documented — inputs, outputs, error codes | Auto-generated from FastAPI + maintained manually |
| **Test Plan** | What will be tested, how, and what passing looks like | Before Phase 5 |
| **MkDocs Site** | All of the above published as a documentation website | End of project |

---

## Order of Work

1. **SRS** — write this before touching any code
2. **SDD** — write this based on the SRS
3. **Phase 1 coding begins**
4. **ADRs** — write one each time a significant technical decision is made
5. **API Reference** — auto-generates as the API is built, maintained alongside it
6. **Test Plan** — written before Phase 5
7. **MkDocs Site** — everything published together at the end

---

## Rules to Follow

- **SRS and SDD come before code.** No exceptions. Retrofitting documents after the fact defeats the purpose.
- **Don't let documentation become procrastination.** Documents serve the project, not the other way around. Each document has a clear exit criteria — once it covers what it needs to, move on.
- **ADRs are short by design.** One page max. They record the decision, the options considered, and why this option was chosen. Nothing more.

---

## Status

- [ ] SRS
- [ ] SDD
- [ ] ADR template created
- [ ] API Reference (in progress during Phase 1–3)
- [ ] Test Plan
- [ ] MkDocs Site

---

## Next Session

Start writing the SRS together.

The SRS will cover:
- Purpose and scope of the system
- Functional requirements (what the system does)
- Non-functional requirements (performance, reliability, scalability)
- User stories
- System constraints
- Assumptions and dependencies

---

*See also: `LEARNING_PLAN.md`, `F1_Integrated_Systems_Project_Plan.pdf`*

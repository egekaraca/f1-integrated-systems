# Learning Plan — F1 Integrated Systems

A personal roadmap to build up to this project without burning out.
Come back to this file whenever you need to reorient.

---

## The Big Picture

| Period | Focus | Goal |
|---|---|---|
| Now → Semester start | Python + Phase 1 of the project | Running project skeleton with data flowing |
| During semester | LLM course + Phase 2–3 of the project | ML models aligned with what you're learning in class |
| End of semester | Polish + documentation + live demo | Portfolio-ready, interview-ready |

---

## Stage 1 — Before Semester Starts

### Goal
Be able to read Python code confidently and have Phase 1 of the project running locally.

No ML required yet. No complex algorithms. Just the foundation.

---

### 1. Python Basics

You need to reach the point where you can read code and understand what it does.
You do not need to memorize syntax or build things from scratch.

**What to cover:**
- Variables, data types, functions, classes
- Importing libraries
- Reading error messages and stack traces
- Basic file I/O

**Resources:**
- [CS50P — Harvard's Python Course (Free)](https://cs50.harvard.edu/python/) — Best free structured Python course. Do the first 4–5 weeks.
- [Automate the Boring Stuff with Python (Free online book)](https://automatetheboringstuff.com/) — Very practical, easy to read. Chapters 1–6 are enough for now.
- [Python in 100 Seconds — Fireship (YouTube)](https://www.youtube.com/watch?v=x7X9w_GIm1s) — 2 minute overview to start with.

**Checkpoint:** You can read a 50-line Python script and explain what each section does, even if you couldn't write it from scratch.

---

### 2. What an API Is

The entire project communicates through APIs. You need to understand the concept, not the code.

**What to cover:**
- What HTTP is (request → response)
- What REST means (GET, POST, endpoints, JSON)
- What a WebSocket is and how it differs from regular HTTP
- How to use a tool like Postman or curl to call an API manually

**Resources:**
- [What is a REST API? — Fireship (YouTube)](https://www.youtube.com/watch?v=-MTSQjw5DrM) — 6 minutes, very clear.
- [HTTP Crash Course — Traversy Media (YouTube)](https://www.youtube.com/watch?v=iYM2zFP3Zn0) — Deeper dive if you want it.
- [FastAPI Official Docs — First Steps](https://fastapi.tiangolo.com/tutorial/first-steps/) — Read this when we start building. Best docs of any framework.

**Checkpoint:** You can explain what happens when a browser visits a URL, what JSON is, and why WebSockets exist.

---

### 3. Docker Basics

You need to be able to start the whole project with one command and read the logs.

**What to cover:**
- What a container is (why it exists, what problem it solves)
- What `docker-compose up` does
- How to read Docker logs when something breaks
- What volumes and ports mean in a docker-compose file

**Resources:**
- [Docker in 100 Seconds — Fireship (YouTube)](https://www.youtube.com/watch?v=Gjnup-PuquQ) — Start here.
- [Docker Tutorial for Beginners — TechWorld with Nana (YouTube)](https://www.youtube.com/watch?v=3c-iBn73dDE) — The best full Docker beginner video. Watch the first 45 minutes.
- [Docker Official Getting Started Guide](https://docs.docker.com/get-started/) — Follow along hands-on.

**Checkpoint:** You can run `docker-compose up`, see the services start, and identify which service threw an error from the logs.

---

### 4. Git & GitHub

You probably know some of this already. Fill any gaps.

**What to cover:**
- `git init`, `git add`, `git commit`, `git push`
- Branches and pull requests (even as a solo developer)
- Reading a git diff

**Resources:**
- [Git in 100 Seconds — Fireship (YouTube)](https://www.youtube.com/watch?v=hwP7WQkmECE)
- [GitHub Skills (Free, interactive)](https://skills.github.com/) — Hands-on exercises directly in GitHub.

**Checkpoint:** You can create a branch, make a commit, open a pull request, and merge it.

---

## Stage 2 — During the Semester

### Goal
Run your LLM course alongside Phases 2 and 3 of the project.
Let the course teach you the concepts, let the project give you context for why they matter.

---

### ML Concepts (Pre-course warm-up, optional but useful)

Watch these before the semester starts if you want a head start.

**Resources:**
- [But what is a neural network? — 3Blue1Brown (YouTube)](https://www.youtube.com/watch?v=aircAruvnKk) — The clearest visual explanation of neural networks that exists.
- [StatQuest with Josh Starmer (YouTube)](https://www.youtube.com/@statquest) — Watch his videos on: Decision Trees, Random Forests, and Neural Networks. Slow-paced, very clear.
- [Murat Karakaya Akademi (YouTube)](https://www.youtube.com/@MuratKarakayaAkademi) — Preview your course material here. Watch whatever aligns with the syllabus.

---

### What to Look for in Your Course

These topics will directly apply to Phase 2 of this project. Pay extra attention when your class covers:

| Course Topic | How It Applies Here |
|---|---|
| Sequence models / RNNs / LSTMs | We use an LSTM for predicting component remaining life |
| Anomaly detection | We use Isolation Forest to flag abnormal sensor readings |
| Training pipelines | You'll recognise the same pattern when I build ours |
| Model evaluation (precision, recall, F1 score) | How we validate whether our maintenance model actually works |
| Transformers / attention | Background context for why LSTMs exist and what replaced them |

---

## Phase 1 Checklist (What We're Building First)

When you're ready to start coding, these are the deliverables for Phase 1.
Everything here can be built before your semester starts.

- [ ] Project repository set up with clean folder structure
- [ ] Telemetry simulator generating sensor data (tire temp, brake wear, engine vibration, oil pressure)
- [ ] PostgreSQL + TimescaleDB database running in Docker
- [ ] Basic FastAPI with health check and telemetry ingestion endpoints
- [ ] Docker Compose file that starts everything with one command
- [ ] GitHub Actions CI pipeline (linting + basic tests)

---

## Useful Tools to Install Now

| Tool | Why |
|---|---|
| [Python 3.11+](https://www.python.org/downloads/) | Backend language |
| [Docker Desktop](https://www.docker.com/products/docker-desktop/) | Run the full stack locally |
| [VS Code](https://code.visualstudio.com/) | Best editor for this stack, works well with Claude |
| [Postman](https://www.postman.com/downloads/) | Test API endpoints manually |
| [TablePlus](https://tableplus.com/) | Browse the database with a GUI (free tier is enough) |

---

## F1 Domain Resources

Understanding the domain makes the project more authentic and interviews easier.

- [FastF1 Documentation](https://docs.fastf1.dev/) — The Python library we use for real F1 data
- [Jolpica F1 API](https://api.jolpi.ca/) — Historical race results and lap times
- [F1 Technical (Website)](https://www.f1technical.net/) — Deep technical articles on F1 car systems
- Watch a race weekend with the technical commentary mindset — pay attention to when teams pit, what compounds they use, and how they talk about tire degradation

---

*Last updated: May 2026*
*Project plan: See `F1_Integrated_Systems_Project_Plan.pdf`*

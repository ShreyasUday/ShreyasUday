<div align="center">

<img src="assets/hero-banner.svg" width="100%" alt="Shreyas Uday: Backend Engineer" />

<br/><br/>

<a href="https://readme-typing-svg.demolab.com/">
  <img src="https://readme-typing-svg.demolab.com/?lines=Backend+Engineer;Systems+%7C+APIs+%7C+Infrastructure;Java+%7C+Node.js+%7C+PostgreSQL+%7C+Docker&font=JetBrains+Mono&center=true&width=520&height=40&color=38BDF8&vCenter=true&size=18&pause=1800" />
</a>

<br/>

<a href="https://shreyas-uday.vercel.app/"><img src="https://img.shields.io/badge/Portfolio-0F172A?style=flat-square&logo=vercel&logoColor=38BDF8" /></a> <a href="https://www.linkedin.com/in/shreyas-uday-530482286/"><img src="https://img.shields.io/badge/LinkedIn-0F172A?style=flat-square&logo=linkedin&logoColor=38BDF8" /></a> <a href="mailto:udayshreyas123@gmail.com"><img src="https://img.shields.io/badge/Email-0F172A?style=flat-square&logo=gmail&logoColor=38BDF8" /></a> <a href="https://github.com/ShreyasUday"><img src="https://img.shields.io/badge/GitHub-0F172A?style=flat-square&logo=github&logoColor=38BDF8" /></a>

</div>

<br/>

<img src="https://img.shields.io/badge/-OPEN%20SOURCE-0F172A?style=flat-square" height="28"/>

> Everything I build is public by default: not released after the fact, developed in the open from the first commit.

`DeployStation`, `ReccoFlix`, `MoodTune`, and `JobTrackr` are all public repositories, not private projects with a polished repo bolted on afterward.

**Current focus**: backend infrastructure and developer tooling: PaaS platforms, deployment pipelines, and the APIs that sit underneath them, mostly in TypeScript/Node.js and Python.

**Where this is headed**: moving from "my repos are public" to actually contributing to projects I use daily. The goal isn't a contributions graph that looks busy; it's a first meaningful PR to an infra or dev-tooling project this year.

**Why**: the parts of engineering I find most interesting (deployment systems, container orchestration, API boundaries) are exactly the parts open source does best. You get to see how other people solved the same infrastructure problem, and where they were wrong about it.

---

<img src="https://img.shields.io/badge/-TECH%20STACK-0F172A?style=flat-square" height="28"/>

<table>
<tr>
<td width="33%" valign="top">

**Languages**

<img src="https://skillicons.dev/icons?i=java,typescript,javascript,python&theme=dark"/>

</td>
<td width="33%" valign="top">

**Backend**

<img src="https://skillicons.dev/icons?i=nodejs,express,fastapi&theme=dark"/>

</td>
<td width="33%" valign="top">

**Databases**

<img src="https://skillicons.dev/icons?i=postgres,redis,prisma&theme=dark"/>

</td>
</tr>
<tr>
<td width="33%" valign="top">

**Cloud & Infra**

<img src="https://skillicons.dev/icons?i=aws,docker,nginx&theme=dark"/>

</td>
<td width="33%" valign="top">

**Tooling**

<img src="https://skillicons.dev/icons?i=git,github,githubactions&theme=dark"/>

</td>
<td width="33%" valign="top">

**Frontend** <sub>(exploring)</sub>

<img src="https://skillicons.dev/icons?i=html,css,react,nextjs&theme=dark"/>

</td>
</tr>
</table>

---

<img src="https://img.shields.io/badge/-APPLIED%20AI-0F172A?style=flat-square" height="28"/>

Not a separate specialty: AI shows up in my backend work where it earns its place.

- **LLM APIs**: Groq-hosted Llama 3.3 70B as the recommendation engine behind `ReccoFlix`
- **RAG & Vector Retrieval**: LlamaIndex + ChromaDB on a team legal-AI project (case analysis over LLaMA 3.1)
- **Inference Pipelines**: Python ML pipeline behind `MoodTune`, served through a dedicated FastAPI microservice
- **Prompt & API Integration**: designing backend services that treat an LLM API as just another dependency to wrap, rate-limit, and fail gracefully around

---

<img src="https://img.shields.io/badge/-PROJECTS-0F172A?style=flat-square" height="28"/>

<details>
<summary><b>DeployStation</b>: PaaS deployment engine for Node.js apps</summary>
<br/>

**Problem**: Deploying a small Node.js app shouldn't require provisioning a server, installing a runtime, and babysitting a process by hand. DeployStation compresses "push to GitHub" into "get a live URL."

**Architecture**: A layered REST API (Controller → Service, deliberately not MVC) sits in front of Docker. Each deployment gets its own isolated container, tracked through a 4-state lifecycle: `pending → building → live → failed`. Runs on a single AWS EC2 instance with per-deployment subdomain generation and GitHub OAuth for auth.

**Interesting engineering decision**: Keeping the Controller and Service layers strictly separate (instead of folding logic into route handlers) made the lifecycle state machine testable on its own, which mattered once "building" started branching into more failure modes than expected.

**Challenges & trade-offs**: Status updates currently run on polling, not push-based updates. That's a known, deliberate trade-off: simple to reason about, but it won't hold up past a handful of concurrent deployments. Moving to SSE/WebSockets is next, not a someday-maybe.

**Lessons learned**: Socket.io and Node's built-in `EventEmitter` solve different problems (transport vs. in-process events). Treating them as interchangeable early cost some rework once real-time tracking became a real requirement instead of a nice-to-have.

**Repository**: [github.com/ShreyasUday/DeployStation](https://github.com/ShreyasUday/DeployStation)

</details>

<details>
<summary><b>ReccoFlix</b>: AI-powered anime recommendation system</summary>
<br/>

**Problem**: Anime discovery across Kitsu and MyAnimeList/Jikan is fragmented: two APIs, two data shapes, no single "what should I watch next."

**Architecture**: A Node.js + Prisma backend normalizes both sources into one PostgreSQL schema, then hands the recommendation step to Groq's hosted Llama 3.3 70B. Deployed on EC2 behind Nginx, kept alive with PM2.

**Interesting engineering decision**: Using Groq specifically for inference (over a slower general-purpose LLM host) keeps recommendation latency low enough that it doesn't feel like a "wait for the AI" step.

**Challenges & trade-offs**: Reconciling two external APIs with different metadata conventions into one schema means accepting that some data will always be slightly stale relative to either source individually.

**Lessons learned**: Designing the schema around what the recommender actually needs (rather than mapping each API's response shape 1:1 into the database) held up better as both sources evolved.

**Repository**: [github.com/ShreyasUday/ReccoFlix](https://github.com/ShreyasUday/ReccoFlix)

</details>

<details>
<summary><b>MoodTune</b>: mood-based music recommendation system</summary>
<br/>

**Problem**: Turning something subjective ("I'm in this kind of mood") into a concrete, ranked list of songs.

**Architecture**: A Python ML pipeline classifies mood, exposed through a FastAPI microservice that a full-stack web app calls for recommendations.

**Interesting engineering decision**: Separating the ML pipeline from the web app behind a dedicated FastAPI boundary kept the recommendation logic swappable without touching the frontend.

**Challenges & trade-offs**: Mood is fuzzy input by nature; the trade-off sits between a simple, fast classifier and a slower, more nuanced one.

**Repository**: [github.com/ShreyasUday/MoodTune-Mood-Based-Music-Recommendation-System](https://github.com/ShreyasUday/MoodTune-Mood-Based-Music-Recommendation-System)

</details>

<details>
<summary><b>JobTrackr</b>: job application tracker (backend + Android)</summary>
<br/>

**Problem**: Job hunting generates more state (applied, interviewing, ghosted, offer) than a spreadsheet comfortably tracks, especially from a phone.

**Architecture**: FastAPI + PostgreSQL backend, Celery/Redis for background work, paired with a native Android client in Kotlin + Jetpack Compose.

**Interesting engineering decision**: Offloading background work to Celery/Redis instead of running it inline keeps the API responsive even while a reminder or status check is running.

**Challenges & trade-offs**: Maintaining a native Android client alongside the backend roughly doubles the surface area to keep in sync: a deliberate trade for a real on-the-go experience over a mobile web view.

**Repository**: *add your repo link here*

</details>

---

<img src="https://img.shields.io/badge/-EXPERIENCE-0F172A?style=flat-square" height="28"/>

> Currently interviewing for SDE and backend engineering internships. This section will list real roles as they're confirmed, not before.

---

<img src="https://img.shields.io/badge/-ACHIEVEMENTS-0F172A?style=flat-square" height="28"/>

<br/>

<div align="left">

<a href="https://github.com/ShreyasUday?tab=achievements">
  <img src="https://img.shields.io/badge/Achievement-Pull_Shark-0F172A?style=flat-square&logo=github&logoColor=38BDF8" height="32" alt="Pull Shark" />
</a>
&nbsp;&nbsp;
<a href="https://github.com/ShreyasUday?tab=achievements">
  <img src="https://img.shields.io/badge/Achievement-YOLO-0F172A?style=flat-square&logo=github&logoColor=67E8F9" height="32" alt="YOLO" />
</a>

</div>

---

<img src="https://img.shields.io/badge/-GITHUB%20ANALYTICS-0F172A?style=flat-square" height="28"/>

<div align="center">

<img src="https://img.shields.io/github/stars/ShreyasUday?style=flat-square&color=38BDF8&labelColor=0F172A&label=Stars" /> <img src="https://img.shields.io/github/followers/ShreyasUday?style=flat-square&color=67E8F9&labelColor=0F172A&label=Followers" />

<br/><br/>

<img src="https://streak-stats.demolab.com/?user=ShreyasUday&theme=dark&hide_border=true&background=00000000&ring=38BDF8&fire=67E8F9&currStreakLabel=38BDF8&sideLabels=94A3B8&sideNums=E2E8F0" width="80%" />

</div>

---

<img src="https://img.shields.io/badge/-CONTRIBUTION%20ACTIVITY-0F172A?style=flat-square" height="28"/>

<div align="center">

<img src="https://github-readme-activity-graph.vercel.app/graph?username=ShreyasUday&theme=minimal&hide_border=true&bg_color=00000000&color=38BDF8&line=38BDF8&point=67E8F9&height=280&area=false" width="90%" />

</div>

---

<img src="https://img.shields.io/badge/-CONTRIBUTION%20SNAKE-0F172A?style=flat-square" height="28"/>

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/Platane/Platane/output/github-contribution-grid-snake-dark.svg" />
  <img src="https://raw.githubusercontent.com/Platane/Platane/output/github-contribution-grid-snake.svg" width="90%" alt="Contribution Snake Grid" />
</picture>

</div>

---

<img src="https://img.shields.io/badge/-CURRENT%20FOCUS-0F172A?style=flat-square" height="28"/>

<table>
<tr>
<td width="33%" valign="top">

**Learning & Systems**
- **Distributed Queues**: Redis & Celery pattern design
- **CI/CD Automation**: GitHub Actions deployment pipelines
- **Systems Language**: Go for high-concurrency microservices

</td>
<td width="33%" valign="top">

**Active Builds**
- **DeployStation**: Upgrading polling to SSE & WebSockets
- **ReccoFlix**: Expanding multi-source recommendation coverage

</td>
<td width="33%" valign="top">

**Exploring & Researching**
- **Applied RAG**: Vector indexing & LLaMA 3 pipelines
- **Architecture**: Distributed system design write-ups
- **Open Source**: First core infra & dev-tooling PR

</td>
</tr>
</table>

---

<img src="https://img.shields.io/badge/-CONNECT-0F172A?style=flat-square" height="28"/>

<div align="center">

<a href="mailto:udayshreyas123@gmail.com"><img src="https://img.shields.io/badge/Gmail-0F172A?style=flat-square&logo=gmail&logoColor=38BDF8" /></a> <a href="https://www.linkedin.com/in/shreyas-uday-530482286/"><img src="https://img.shields.io/badge/LinkedIn-0F172A?style=flat-square&logo=linkedin&logoColor=38BDF8" /></a> <a href="https://github.com/ShreyasUday"><img src="https://img.shields.io/badge/GitHub-0F172A?style=flat-square&logo=github&logoColor=38BDF8" /></a> <a href="https://shreyas-uday.vercel.app/"><img src="https://img.shields.io/badge/Portfolio-0F172A?style=flat-square&logo=vercel&logoColor=38BDF8" /></a>

</div>

<br/>

<div align="center">

<sub>Ship it. Monitor it. Make it boring.</sub>

</div>

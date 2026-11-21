<!-- HEADER BANNER -->
<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:0d1117,45:6e40c9,100:00d4ff&height=210&section=header&text=Minh%20Hi%E1%BB%83n&fontSize=52&fontColor=ffffff&fontAlignY=38&desc=Backend%20%26%20Distributed%20Systems%20Engineer%20%E2%80%A2%20HCMUS&descAlignY=60&descSize=16&animation=fadeIn" width="100%" />

  <!-- TYPING ANIMATION -->
  <a href="https://github.com/Hiendro11">
    <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=18&duration=2800&pause=1000&color=00D4FF&center=true&vCenter=true&width=680&lines=Building+backend+systems+where+failure+is+a+design+concern.;Making+race+conditions+impossible%2C+not+just+unlikely.;Durable+Workflows+%E2%80%A2+Atomic+Transactions+%E2%80%A2+Real+Payments." alt="Typing SVG" />
  </a>

  <br/>

  <!-- CONTACT BADGES -->
  <a href="mailto:txm.hien1102@gmail.com">
    <img src="https://img.shields.io/badge/Email-txm.hien1102@gmail.com-EA4335?style=for-the-badge&logo=gmail&logoColor=white" />
  </a>
  <a href="https://www.linkedin.com/in/hienkul/">
    <img src="https://img.shields.io/badge/LinkedIn-hienkul-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" />
  </a>
  <img src="https://img.shields.io/badge/Focus-Concurrency%20%26%20Reliability-8A2BE2?style=for-the-badge&logo=prometheus&logoColor=white" />
</div>

<br/>

---

## ⚡ System Diagnostics (`$ whoami`)

```yaml
engineer:    "Minh Hiển"
education:   "Ho Chi Minh City University of Science (HCMUS)"
core_focus:  "High-Contention Backend, Durable Workflows & Payment Systems"

engineering_principles:
  - "Failure is a design concern, not an afterthought."
  - "No complexity for résumé points — only what the problem demands."
  - "Prove concurrency safety with load tests, not assumptions."
```

<br/>

---

## 🛠️ Tech Arsenal

<div align="center">
  <!-- Main Visual Stack -->
  <img src="https://skillicons.dev/icons?i=java,go,kotlin,ts,py,spring,nestjs,django,express&perline=9" /><br/>
  <img src="https://skillicons.dev/icons?i=mysql,postgres,mongodb,redis,prisma,hibernate,docker,git,maven&perline=9" />

  <br/><br/>

  <!-- Specialized Production Tools & Patterns -->
  <img src="https://img.shields.io/badge/Temporal_Workflows-141414?style=for-the-badge&logo=temporal&logoColor=00D4FF" />
  <img src="https://img.shields.io/badge/WebSocket_&_Socket.io-010101?style=for-the-badge&logo=socketdotio&logoColor=white" />
  <img src="https://img.shields.io/badge/Celery_Workers-37814A?style=for-the-badge&logo=celery&logoColor=white" />
  <img src="https://img.shields.io/badge/ZaloPay_Production_SDK-0068FF?style=for-the-badge&logo=zalo&logoColor=white" />
  <img src="https://img.shields.io/badge/Twilio_SMS-F22F46?style=for-the-badge&logo=twilio&logoColor=white" />
  <img src="https://img.shields.io/badge/Firebase_FCM-FFCA28?style=for-the-badge&logo=firebase&logoColor=black" />
  <img src="https://img.shields.io/badge/Jetpack_Compose-4285F4?style=for-the-badge&logo=jetpackcompose&logoColor=white" />
</div>

<br/>

---

## 🚀 Engineered Systems (`$ ls -la ~/projects/`)

> Click on any project below to inspect the architecture, concurrency problem, and solution.

<details open>
<summary>
  <b>🟣 tracking-order</b> — E-Commerce Order & Logistics Platform &nbsp;
  <img src="https://img.shields.io/badge/Optimistic_Locking-6e40c9?style=flat-square" />
  <img src="https://img.shields.io/badge/Idempotent_Checkout-00a8e8?style=flat-square" />
</summary>

<br/>

| | |
|---|---|
| **Stack** | `Java 17` · `Spring Boot 4.x` · `Hibernate JPA` · `MySQL 8.4` |
| **Architecture** | Layered monolith with transactional checkout pipeline |

**The problem:** Two users click "Buy" on the last item at the same time. Who gets it?

**My solution:**
- **Optimistic locking** (`@Version`) on inventory: the second transaction gets `ObjectOptimisticLockingFailureException`, never negative stock.
- **Two-phase inventory reservation:** `ACTIVE → COMMITTED → RELEASED` lifecycle prevents ghost holds.
- **Idempotent checkout:** network retries with the same `checkout_request_key` return the original order, never a duplicate.
- **Dynamic shipping engine:** rates computed from weight × region × carrier rules, with auto-freeship thresholds.

```text
POST /api/v1/checkout  →  Validate Cart → Check Inventory → Reserve Stock
                          → Apply Promotion → Calculate Shipping → Create Order
                          → Commit Reservation → Return Response
```

<p align="right"><a href="https://github.com/Hiendro11/tracking-order"><img src="https://img.shields.io/badge/Inspect_Source-181717?style=for-the-badge&logo=github" /></a></p>

</details>

<br/>

<details open>
<summary>
  <b>🔵 Remote-Patient-Monitoring</b> — Healthcare Telemetry Platform &nbsp;
  <img src="https://img.shields.io/badge/Temporal_Workflows-00ADD8?style=flat-square" />
  <img src="https://img.shields.io/badge/Real--Time_WebSocket-ff007f?style=flat-square" />
</summary>

<br/>

| | |
|---|---|
| **Stack** | `Go 1.24` · `Gin` · `Temporal SDK` · `MongoDB` · `Redis` · `WebSocket` |
| **Clients** | Doctor Web (React/Vite) · Patient Mobile (React Native/Expo) · Admin Web & Mobile |

**The problem:** A patient misses their medication at 3 AM. The system must notice, escalate, and never forget — even if the server restarts.

**My solution:**
- **Temporal Durable Workflows:** medication reminders and alert evaluation run as long-lived workflows that survive process crashes and deployments.
- **Real-time vital streaming:** WebSocket pipeline pushes heart rate, SpO2, and blood pressure to doctors instantly.
- **Multi-tenant auth:** Google OAuth2 for patients, JWT for doctors, and role-based API gates.
- **Twilio SMS escalation:** critical vitals trigger SMS to the attending physician within seconds.

```text
Sensor → WebSocket → Go Backend → Temporal Workflow (evaluate vitals)
                                 ├── Normal   → Log & Dashboard
                                 └── Critical → Twilio SMS + Push Notification
```

<p align="right"><a href="https://github.com/Hiendro11/Remote-Patient-Monitoring"><img src="https://img.shields.io/badge/Inspect_Source-181717?style=for-the-badge&logo=github" /></a></p>

</details>

<br/>

<details>
<summary>
  <b>🔴 concert-ticket-booking</b> — High-Contention Ticketing System &nbsp;
  <img src="https://img.shields.io/badge/Atomic_SQL-E0234E?style=flat-square" />
  <img src="https://img.shields.io/badge/0_Oversell-2ea44f?style=flat-square" />
</summary>

<br/>

| | |
|---|---|
| **Stack** | `Node.js 22` · `NestJS` · `TypeScript` · `Prisma 7` · `MySQL 8.4` · `Docker` |
| **Context** | GEEK Internship Autumn 2026 — Backend Engineer Assessment |

**The problem:** 10,000 fans try to buy the last 100 concert tickets simultaneously. Zero overselling allowed.

**My solution:**
- **Atomic conditional decrement** at the SQL level (`UPDATE ... SET available = available - ? WHERE available >= ?`) — zero application-level locks needed.
- **Custom concurrency test suite:** bash scripts firing parallel requests to prove inventory race ✓, voucher quota race ✓, duplicate race ✓, and cancel race ✓.
- **Modular monolith:** clean domain separation (concerts / bookings / operations) with Prisma.

```text
100 concurrent requests → Atomic SQL deduction → Exactly 100 succeed, 0 oversell
```

<p align="right"><a href="https://github.com/Hiendro11/concert-ticket-booking"><img src="https://img.shields.io/badge/Inspect_Source-181717?style=for-the-badge&logo=github" /></a></p>

</details>

<br/>

<details>
<summary>
  <b>🟢 Mobile-MusicApp</b> — Android Music Streaming + ZaloPay &nbsp;
  <img src="https://img.shields.io/badge/Real_VND_Payments-0068FF?style=flat-square" />
  <img src="https://img.shields.io/badge/Kotlin_SDK_35-7F52FF?style=flat-square" />
</summary>

<br/>

| | |
|---|---|
| **Stack** | `Kotlin` · `Android SDK 35` · `DataBinding` · `Navigation SafeArgs` · `ZaloPay SDK` |

**What makes it different:** Real payment integration. Not Stripe-in-sandbox, but **ZaloPay production SDK** (`zpdk-release-v3.1.aar`) handling actual VND transactions for premium subscriptions.

- Real-time lyrics synchronization scrolling word-by-word with playback position.
- Sleep timer with background service lifecycle management.
- Full playlist CRUD with drag-to-reorder.

<p align="right"><a href="https://github.com/Hiendro11/Mobile-MusicApp"><img src="https://img.shields.io/badge/Inspect_Source-181717?style=for-the-badge&logo=github" /></a></p>

</details>

<br/>

<details>
<summary>
  <b>🟡 YouManage</b> — Real-time Project Management (Jira/Trello-like) &nbsp;
  <img src="https://img.shields.io/badge/Django_Channels-092E20?style=flat-square" />
  <img src="https://img.shields.io/badge/Celery_Workers-37814A?style=flat-square" />
</summary>

<br/>

| | |
|---|---|
| **Backend** | `Python` · `Django 5.1` · `Celery 5.4` · `Django Channels/Daphne` · `Redis` · `SimpleJWT` |
| **Mobile** | `Kotlin` · `Jetpack Compose` · `150+ source files` · `Firebase Cloud Messaging` |

- WebSocket-powered real-time project chat and notifications via Django Channels.
- Background task processing (email, report export) through Celery workers.
- Full issue lifecycle: Create → Assign → Track → Change Request → Audit Log.
- Push notifications via FCM to native Android client.

<p align="right"><a href="https://github.com/bktrung/intro2se-cq22_3-group15"><img src="https://img.shields.io/badge/Inspect_Source-181717?style=for-the-badge&logo=github" /></a></p>

</details>

<br/>

<details>
<summary>
  <b>🟠 MentorHub</b> — Mentor-Mentee Matching & Live Chat &nbsp;
  <img src="https://img.shields.io/badge/End--to--End_TS-3178C6?style=flat-square" />
  <img src="https://img.shields.io/badge/Socket.io-010101?style=flat-square" />
</summary>

<br/>

| | |
|---|---|
| **Stack** | `TypeScript` · `Express.js` · `Zod` · `MongoDB/Mongoose` · `Socket.io` · `React 19` · `Vite` |

- End-to-end TypeScript from API validation (Zod schemas) to React components.
- Multi-room real-time messaging with Socket.io and online/offline presence tracking.
- Role-based access control with mentor scheduling and trust scoring.

<p align="right"><a href="https://github.com/Hiendro11/MentorHub"><img src="https://img.shields.io/badge/Inspect_Source-181717?style=for-the-badge&logo=github" /></a></p>

</details>

<br/>

<!-- FOOTER WAVE -->
<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:00d4ff,55:6e40c9,100:0d1117&height=120&section=footer&text=Ship%20it%20correct%2C%20or%20don't%20ship%20it%20at%20all.&fontSize=18&fontColor=ffffff&fontAlignY=65" width="100%" />
</div>

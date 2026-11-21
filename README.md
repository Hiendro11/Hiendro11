<div align="center">

```
████████╗██████╗  ██╗ ██╗██╗   ██╗ █████╗ ███╗   ██╗    ███╗   ███╗██╗███╗   ██╗██╗  ██╗    ██╗  ██╗██╗ ██████╗██╗███╗   ██╗
╚══██╔══╝██╔══██╗██║ ██║██║   ██║██╔══██╗████╗  ██║    ████╗ ████║██║████╗  ██║██║  ██║    ██║  ██║██║██╔════╝██║████╗  ██║
   ██║   ██████╔╝███████║██║   ██║███████║██╔██╗ ██║    ██╔████╔██║██║██╔██╗ ██║███████║    ███████║██║█████╗  ██║██╔██╗ ██║
   ██║   ██╔══██╗██╔══██║██║   ██║██╔══██║██║╚██╗██║    ██║╚██╔╝██║██║██║╚██╗██║██╔══██║    ██╔══██║██║██╔══╝  ██║██║╚██╗██║
   ██║   ██║  ██║██║  ██║╚██████╔╝██║  ██║██║ ╚████║    ██║ ╚═╝ ██║██║██║ ╚████║██║  ██║    ██║  ██║██║██████╗ ██║██║ ╚████║
   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═══╝    ╚═╝     ╚═╝╚═╝╚═╝  ╚═══╝╚═╝  ╚═╝    ╚═╝  ╚═╝╚═╝╚═════╝╚═╝╚═╝  ╚═══╝
```

**`Software Engineer`** · Ho Chi Minh City University of Science (HCMUS)

*I don't just write code — I engineer systems that survive production.*

<br>

[![Email](https://img.shields.io/badge/-txm.hien1102@gmail.com-c14438?style=flat&logo=Gmail&logoColor=white)](mailto:txm.hien1102@gmail.com)&nbsp;&nbsp;
[![GitHub](https://img.shields.io/badge/-Hiendro11-181717?style=flat&logo=github)](https://github.com/Hiendro11)

</div>

---

<br>

## `$ whoami`

```json
{
  "name": "Trần Xuân Minh Hiển",
  "role": "Software Engineer",
  "university": "HCMUS — Ho Chi Minh City University of Science",
  "languages": ["Java", "Go", "Kotlin", "TypeScript", "Python"],
  "obsessions": [
    "Making race conditions impossible, not just unlikely",
    "Distributed workflows that never lose state",
    "Payment integrations that handle real money safely"
  ]
}
```

<br>

## `$ cat philosophy.md`

> **I build backend systems where failure is a design concern, not an afterthought.**
>
> Every project below solves a real concurrency or reliability problem — optimistic locking under high contention, durable workflows that survive crashes, atomic inventory deductions that prevent overselling. I don't add complexity for résumé points; I add it because the problem demands it.

<br>

---

<br>

## `$ ls -la ~/projects/`

<br>

<details open>
<summary><b>🔶 tracking-order</b> — E-Commerce Order & Logistics Platform</summary>

<br>

| | |
|---|---|
| **Stack** | `Java 17` · `Spring Boot 4.x` · `Hibernate JPA` · `MySQL 8.4` |
| **Architecture** | Layered monolith with transactional checkout pipeline |

**The problem:** Two users click "Buy" on the last item at the same time. Who gets it?

**My solution:**
- **Optimistic locking** (`@Version`) on inventory — the second transaction gets `ObjectOptimisticLockingFailureException`, not negative stock
- **Two-phase inventory reservation** — `ACTIVE → COMMITTED → RELEASED` lifecycle prevents ghost holds
- **Idempotent checkout** — network retries with the same `checkout_request_key` return the original order, never a duplicate
- **Dynamic shipping engine** — rates computed from weight × region × carrier rules, with auto-freeship thresholds

```
POST /api/v1/checkout  →  Validate Cart → Check Inventory → Reserve Stock
                          → Apply Promotion → Calculate Shipping → Create Order
                          → Commit Reservation → Return Response
```

<p align="right"><a href="https://github.com/Hiendro11/tracking-order"><code>→ source</code></a></p>

</details>

<br>

<details open>
<summary><b>🔶 Remote-Patient-Monitoring</b> — Healthcare Telemetry Platform</summary>

<br>

| | |
|---|---|
| **Stack** | `Go 1.24` · `Gin` · `Temporal SDK` · `MongoDB` · `Redis` · `WebSocket` |
| **Clients** | Doctor Web (React/Vite) · Patient Mobile (React Native/Expo) · Admin Web · Admin Mobile |

**The problem:** A patient misses their medication at 3 AM. The system must notice, escalate, and never forget — even if the server restarts.

**My solution:**
- **Temporal Durable Workflows** — medication reminders and alert evaluation run as long-lived workflows that survive process crashes and deployments
- **Real-time vital streaming** via WebSocket — doctors see heart rate / SpO2 / blood pressure the moment sensors push data
- **Multi-tenant auth** — Google OAuth2 for patients, JWT for doctors, role-based API gates
- **Twilio SMS escalation** — critical vitals trigger SMS to the attending physician within seconds

```
Sensor → WebSocket → Go Backend → Temporal Workflow (evaluate vitals)
                                 ├── Normal → Log & Dashboard
                                 └── Critical → Twilio SMS + Push Notification
```

<p align="right"><a href="https://github.com/Hiendro11/Remote-Patient-Monitoring"><code>→ source</code></a></p>

</details>

<br>

<details>
<summary><b>🔶 concert-ticket-booking</b> — High-Contention Ticketing System</summary>

<br>

| | |
|---|---|
| **Stack** | `Node.js 22` · `NestJS` · `TypeScript` · `Prisma 7` · `MySQL 8.4` · `Docker` |
| **Context** | GEEK Internship Autumn 2026 — Backend Engineer Assessment |

**The problem:** 10,000 fans try to buy the last 100 concert tickets simultaneously. Zero overselling allowed.

**My solution:**
- **Atomic conditional decrement** at the SQL level — `UPDATE ... SET available = available - ? WHERE available >= ?` — no application-level locks needed
- **Custom concurrency test suite** — bash scripts that fire parallel requests to prove: inventory race ✓, voucher quota race ✓, same-user duplicate race ✓, cancel race ✓
- **Modular monolith** — clean domain separation (concerts / bookings / operations) with Prisma as the data layer

```
100 concurrent requests → Atomic SQL deduction → Exactly 100 succeed, 0 oversell
```

<p align="right"><a href="https://github.com/Hiendro11/concert-ticket-booking"><code>→ source</code></a></p>

</details>

<br>

<details>
<summary><b>🔶 Mobile-MusicApp</b> — Android Music Streaming + ZaloPay</summary>

<br>

| | |
|---|---|
| **Stack** | `Kotlin` · `Android SDK 35` · `DataBinding` · `Navigation SafeArgs` · `ZaloPay SDK` |

**What makes it different:** Real payment integration. Not Stripe-in-sandbox, but **ZaloPay production SDK** (`zpdk-release-v3.1.aar`) handling actual VND transactions for premium subscriptions.

- Real-time lyrics synchronization — lyrics scroll word-by-word in sync with playback position
- Sleep timer with background service lifecycle management
- Full playlist CRUD with drag-to-reorder

<p align="right"><a href="https://github.com/Hiendro11/Mobile-MusicApp"><code>→ source</code></a></p>

</details>

<br>

<details>
<summary><b>🔶 YouManage</b> — Real-time Project Management (Jira/Trello-like)</summary>

<br>

| | |
|---|---|
| **Backend** | `Python` · `Django 5.1` · `Celery 5.4` · `Django Channels/Daphne` · `Redis` · `SimpleJWT` |
| **Mobile** | `Kotlin` · `Jetpack Compose` · `150+ source files` · `Firebase Cloud Messaging` |

- WebSocket-powered real-time project chat and notifications via Django Channels
- Background task processing (email, report export) through Celery workers
- Full issue lifecycle: Create → Assign → Track → Change Request → Audit Log
- Push notifications via FCM to native Android client

<p align="right"><a href="https://github.com/bktrung/intro2se-cq22_3-group15"><code>→ source</code></a></p>

</details>

<br>

<details>
<summary><b>🔶 MentorHub</b> — Mentor-Mentee Matching & Live Chat</summary>

<br>

| | |
|---|---|
| **Stack** | `TypeScript` · `Express.js` · `Zod` · `MongoDB/Mongoose` · `Socket.io` · `React 19` · `Vite` |

- End-to-end TypeScript from API validation (Zod schemas) to React components
- Multi-room real-time messaging with Socket.io — online/offline presence tracking
- Role-based access control with mentor scheduling and trust scoring

<p align="right"><a href="https://github.com/Hiendro11/MentorHub"><code>→ source</code></a></p>

</details>

<br>

---

<br>

## `$ cat tech-radar.yml`

```yaml
backend:
  production: [Spring Boot, Gin (Go), NestJS, Django, Express.js]
  patterns:  [Optimistic Locking, Idempotency Keys, Saga/Workflow Orchestration, CQRS-lite]

mobile:
  native:    [Kotlin + Jetpack Compose, Kotlin + XML/DataBinding]
  hybrid:    [React Native + Expo]

data:
  relational: [MySQL 8.4, PostgreSQL]
  document:   [MongoDB]
  cache:      [Redis]
  orm:        [Hibernate/JPA, Prisma, Mongoose, Django ORM]

infrastructure:
  runtime:    [Temporal Workflows, Celery + Redis]
  realtime:   [WebSocket, Django Channels, Socket.io]
  messaging:  [Firebase Cloud Messaging, Twilio SMS]
  payments:   [ZaloPay SDK]
  devops:     [Docker Compose, Git, Maven, Swagger/OpenAPI]
```

<br>

---

<br>

## `$ git log --oneline --graph`

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=Hiendro11&show_icons=true&theme=github_dark&hide_border=true&count_private=true&include_all_commits=true&bg_color=00000000&title_color=58a6ff&icon_color=58a6ff&text_color=c9d1d9" height="165" alt="stats" />
  &nbsp;
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=Hiendro11&layout=compact&theme=github_dark&hide_border=true&bg_color=00000000&title_color=58a6ff&text_color=c9d1d9" height="165" alt="languages" />
</p>

<p align="center">
  <img src="https://github-readme-streak-stats.herokuapp.com/?user=Hiendro11&theme=github-dark-blue&hide_border=true&background=00000000&stroke=30363d&ring=58a6ff&fire=58a6ff&currStreakLabel=58a6ff" alt="streak" />
</p>

<br>

---

<div align="center">

<br>

```
                    "Ship it correct, or don't ship it at all."
```

<sub>Built with obsessive attention to concurrency, reliability, and clean architecture.</sub>

<br><br>

</div>

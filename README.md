# CloudOps Agent

> An autonomous AI-powered Cloud Operations Engineer that monitors infrastructure, investigates incidents, identifies root causes, and recommends or performs corrective actions.

CloudOps Agent acts like an intelligent Site Reliability Engineer (SRE) — instead of engineers manually watching dashboards, digging through logs, and troubleshooting alerts, the agent continuously observes the cloud environment and automates the investigation-to-resolution workflow.

## What it does

- 🔍 **Monitors** infrastructure health (CPU, memory, disk, network, error rates, deployments)
- ⚠️ **Detects anomalies** before they become major incidents
- 🕵️ **Investigates** incidents by checking metrics, logs, deployments, and service health — not guessing
- 🧠 **Identifies root causes** by correlating signals across interconnected components
- ✅ **Recommends actions**, gated behind human approval
- ⚙️ **Executes** approved remediations and verifies the outcome
- 📄 **Reports** on every incident and maintains operational history

The goal isn't a chatbot for cloud infrastructure — it's an agent that can observe, reason, investigate, and act.

```
Observe → Investigate → Correlate → Reason → Root Cause →
Recommend → Approval → Execute → Verify → Report
```

## Example

> **Instruction:** "Investigate why the API response time increased."

The agent checks CPU, memory, network, database latency, application logs, and recent deployments — then reports:

```
Finding: Latency increase started immediately after deployment v2.4.1.
Probable cause: Recent application deployment.
```

## Tech Stack

| Layer | Technology |
|---|---|
| Language | TypeScript / Node.js |
| LLM Providers | Gemini (`@google/generative-ai`), Grok (OpenAI-compatible API via `openai` SDK) |
| Agent Orchestration | Custom state machine + provider abstraction (`AgentLLM` interface) |
| Database | PostgreSQL + Prisma |
| Job Scheduling | BullMQ (Redis) |
| Backend API | Fastify |
| Frontend | Next.js + Tailwind + shadcn/ui |
| Realtime | WebSockets / SSE |
| Charts | Recharts |
| Cloud SDK | AWS SDK v3 (extendable to GCP/Azure) |
| Tracing | OpenTelemetry |

See [`cloudops-agent-implementation-plan.md`](./cloudops-agent-implementation-plan.md) for the full phased build plan.

## Project Structure

```
cloudops-agent/
├── apps/
│   ├── api/              # Fastify backend, agent orchestration, webhooks
│   └── web/               # Next.js dashboard
├── packages/
│   ├── agent-core/        # LLM provider adapters, state machine, tool definitions
│   └── db/                 # Prisma schema + client
├── docker-compose.yml      # Postgres + Redis for local dev
└── cloudops-agent-implementation-plan.md
```

## Getting Started

### Prerequisites

- Node.js 20+
- pnpm
- Docker (for local Postgres + Redis)
- API keys: `GEMINI_API_KEY`, `GROK_API_KEY` (xAI)
- AWS credentials with read access to CloudWatch/EC2 (or your target cloud provider)

### Setup

```bash
# install dependencies
pnpm install

# copy env template and fill in keys
cp .env.example .env

# start local Postgres + Redis
docker compose up -d

# run Prisma migrations
pnpm --filter db prisma migrate dev

# start the API
pnpm --filter api dev

# start the dashboard
pnpm --filter web dev
```

### Environment Variables

```
GEMINI_API_KEY=
GROK_API_KEY=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_REGION=
DATABASE_URL=
REDIS_URL=
SLACK_BOT_TOKEN=      # for approval notifications
```

## Safety Model

The agent **never executes actions directly**. Recommendations pass through an explicit human-approval gate (Slack or dashboard) before anything runs. The execution layer is a separate module from the reasoning layer, with its own allow-listed, permission-checked action set — the LLM cannot call infrastructure-mutating functions on its own.

## Status

🚧 Early development — see the [implementation plan](./cloudops-agent-implementation-plan.md) for current phase and roadmap.

## License

TBD

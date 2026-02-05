# Implementation Plan: Slack → GitHub Triage Service (beads-web)

## Context
- Source spec: `docs/spec-slack-github-triage.md` (from OpenHands-Tab PR #944).
- Target app: `enyst/beads-web` (this repo).

## Plan

### Phase 0: Product/infra decisions
1. Confirm deploy target (container + platform) and URL used for Slack Events API request URL.
2. Confirm GitHub auth will use a GitHub App install flow (no OAuth app).
3. Choose storage: start with SQLite for local/dev, Postgres in production (migrations via Alembic).
4. Confirm LLM provider strategy (OpenAI/Anthropic/etc) and client library.

### Phase 1: Project scaffolding
1. Add FastAPI app with `/healthz` route.
2. Add config module for env vars (Pydantic settings).
3. Wire structured logging and request IDs.

### Phase 2: Slack event intake
1. Implement `/slack/events` endpoint handling `url_verification` challenge.
2. Validate Slack signatures (timestamp + signature header).
3. Extract `event_id`, `event_time`, `event.type`, `event.subtype`.
4. Persist raw events and minimal message records (channel, user, ts).
5. Background processing (FastAPI BackgroundTasks for MVP, queue later).

### Phase 3: Slack context fetching
1. Resolve channel metadata via `conversations.info` (cache name → id).
2. For `thread_ts`, fetch replies via `conversations.replies` as needed.
3. Resolve user info for reporter (for issue body).

### Phase 4: Classification pipeline
1. Low-content regex short-circuit.
2. Build LLM prompt (message + thread context + channel metadata).
3. Parse LLM JSON response into schema (validate fields).
4. Persist classification results to message + thread records.

### Phase 5: Unanswered detection + issue creation
1. Evaluate thread unanswered heuristics (maintainer list + reply length).
2. Select repo candidate (classification confidence + heuristics).
3. Create GitHub issue via GitHub App installation token.
4. Persist issue link and mark thread answered.
5. Add `:+1:` reaction to root Slack message only after issue creation succeeds (skip for low-content/no-issue classifications).

### Phase 6: Observability + ops
1. Metrics: events received/processed, LLM latency, issues created.
2. Error handling and retries for Slack and GitHub APIs.
3. Protect sensitive data in logs.
4. Implement rate-limit handling with exponential backoff, jitter, and max retry budget for Slack/GitHub/LLM APIs.


## Questions / Clarifications
1. Which channels in Liberty Labs should be in scope initially? (Need allowlist/denylist config.)
2. What is the canonical list of maintainer Slack user IDs for unanswered detection? Where should it live (env var vs config file)?
3. Should we store full Slack payloads (for auditing) or only normalized message/thread records?
4. Is there a preferred LLM provider and prompt template style (system + user messages vs a single prompt)?

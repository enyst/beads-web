# PRD: Slack → GitHub Triage Service

## Problem
Slack discussions that require engineering follow-up are easy to miss, leading to delayed issue creation and lost context.

## Goals
- Convert qualified Slack threads into GitHub issues with minimal manual effort.
- Preserve context via links back to the original Slack thread.
- Reduce triage load for maintainers.

## Non-goals
- Full Slack UI or interactive workflow inside Slack.
- Multi-tenant Slack/GitHub support in the first release.

## Users
- Maintainers in the Liberty Labs workspace
- Contributors reporting bugs or feature requests in Slack

## Success Metrics
- % of qualifying threads that result in issues
- Time from Slack message to issue creation
- Maintainer-reported reduction in missed requests

## Scope (MVP)
- Ingest Slack message events for selected channels (default allowlist: `#openhands`, `#agents`, `#support`).
- Classify threads via LLM and determine repo routing.
- Create GitHub issues via GitHub App installation tokens.
- React in Slack only after issue creation succeeds.

## Defaults
- Maintainer IDs: `MAINTAINER_SLACK_IDS` env var (comma-separated); start with bot + core maintainers.
- Data retention: keep message text 60 days, redact emails/phone numbers on write.
- Rate limiting: exponential backoff with jitter, max 5 retries, honor `Retry-After`.

## Risks / Open Questions
- Confirm initial channel allowlist and maintainer list values.
- Confirm legal/privacy requirements for retention and redaction.

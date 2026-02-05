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
- Ingest Slack message events for selected channels.
- Classify threads via LLM and determine repo routing.
- Create GitHub issues via GitHub App installation tokens.
- React in Slack only after issue creation succeeds.

## Risks / Open Questions
- Final channel allowlist and maintainer ID list
- Data retention and PII redaction requirements
- Rate-limit budget for Slack/GitHub/LLM providers

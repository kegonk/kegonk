# Voice Business Assistant — Public Case Study

> Private source repository. This page describes the architecture, engineering decisions, and testable behavior without publishing client/discovery materials or private source history.

## Overview

Voice Business Assistant is a demo-first AI business assistant built to validate a realistic voice workflow before connecting real customer data or production telephony.

The system combines a Python/FastAPI backend with OpenAI Realtime over WebRTC, typed tool calls, synthetic customer memory, lead capture, human handoff policy, transcript capture, call state, and a lightweight owner dashboard.

The project deliberately uses synthetic runtime data so the interaction model can be tested without exposing real customer records.

## Architecture

```text
Browser microphone
      |
      | WebRTC audio
      v
OpenAI Realtime
      |
      | function calls / control events
      v
Browser control channel
      |
      v
FastAPI backend
  |-- customer lookup
  |-- lead capture
  |-- handoff policy
  |-- transcript capture
  |-- call state
  `-- owner dashboard
```

## What I built

- FastAPI backend for call/session and tool APIs.
- OpenAI Realtime WebRTC session bootstrap.
- SDP validation and controlled upstream error mapping.
- AI tool contracts for:
  - customer lookup;
  - lead capture;
  - human handoff request.
- Pydantic validation for tool arguments and call payloads.
- Synthetic customer memory for safe demos.
- Per-call state and transcript storage.
- Tool execution log.
- Simple owner-facing call history/dashboard.
- Business prompt boundaries designed to avoid unsupported claims and overreach.
- Tests for business logic and Realtime integration failure modes.

## Reliability and safety behavior

The demo is intentionally constrained rather than pretending to be production-ready.

Examples of implemented safeguards:

- malformed tool arguments return validation errors;
- unknown tools fail closed;
- call state is isolated between sessions;
- finalize behavior is idempotent;
- invalid SDP is rejected before calling the upstream provider;
- upstream transport failures are mapped to controlled backend errors;
- customer data used by the runnable demo is explicitly synthetic;
- the assistant is not given generic database or shell authority.

## Test coverage highlights

The test suite validates behaviors such as:

- health/demo-mode state;
- synthetic customer lookup;
- lead capture;
- handoff request validation;
- OpenAI Realtime session payload construction;
- behavior when the API key is missing;
- invalid SDP rejection;
- upstream connection failure handling;
- malformed tool argument rejection;
- per-call state isolation;
- idempotent finalization;
- fail-closed behavior for unknown tools.

## Technology

- Python
- FastAPI
- Pydantic
- OpenAI Realtime API
- WebRTC
- HTTPX
- REST APIs
- pytest
- Browser JavaScript / Web Audio
- AI tool calling

## Product thinking

The project follows a **demo -> trust -> discovery -> custom pilot** approach.

Instead of starting with a large production integration, the first version proves:

1. whether the voice interaction is useful;
2. whether the assistant follows business boundaries;
3. whether tool calls capture structured leads correctly;
4. when the assistant should escalate to a human;
5. what data and integrations are actually needed for a real pilot.

## Deliberate boundaries

The current demo does **not** claim to provide:

- production telephony;
- real CRM/customer data;
- real human transfer;
- voice cloning;
- production RBAC;
- persistent production storage;
- finished compliance/retention policy.

Those were intentionally left outside the first proof-of-concept.

## Why this project is relevant

This project demonstrates practical AI application engineering rather than model research: connecting a realtime model to a typed backend, controlling tool authority, validating inputs, managing per-session state, testing failure paths, and translating a business workflow into an AI-assisted product.

It complements my VoxProxy project, which focuses more heavily on programmable telephony, real-time audio pipelines, STT/TTS providers, outbound call missions, and Telegram-assisted escalation.

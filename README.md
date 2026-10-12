# Watchman

> "So you, son of man, I have made you a watchman for the house of Israel" — Ezekiel 33:7

> "On your walls, O Jerusalem, I have set watchmen; all the day and all the night they shall never be silent" — Isaiah 62:6

> "For this is the will of God, your sanctification: that you abstain from sexual immorality." — 1 Thessalonians 4:3-5

Watchman is a local-first, system-wide Windows content filtering platform that uses domain reputation, DNS intelligence, URL heuristics, caching, and AI-powered image classification to help block pornographic content across browsers and desktop applications.

## Vision

Provide fast, privacy-preserving protection against pornographic content while keeping all analysis and decision-making on the user's device.

## Core Principles

- Local-first processing
- System-wide protection
- Low-latency decisions
- Explainable classifications
- Aggressive caching
- User-controlled overrides
- AI as a last resort, not a first response
- [Behaviour Contract Driven Development (BCDD)](https://github.com/adadevoh/bcdd/blob/main/bcdd.md)

## Roadmap

### Iteration 1
- Domain blocking
- DNS reputation
- URL heuristics
- SQLite persistence
- Known-good and known-bad caching

### Iteration 2
- Image sampling
- ONNX-based image classification
- Confidence-based content decisions
- Detection of previously unknown adult sites

### Iteration 3
- Browser extension
- DOM and metadata analysis
- Page-level filtering for Reddit, forums, and social platforms
- Service-assisted content classification

## Technology Stack

### Core Platform
- C++20
- Windows Service
- SQLite
- Boost.Asio
- spdlog
- nlohmann/json

### AI & Computer Vision
- ONNX Runtime (Iteration 2)
- OpenCV (Iteration 2)

### Dashboard
- C#
- .NET

### Browser Integration
- TypeScript
- Chrome Extension APIs
- Edge Extension APIs

## Architecture at a Glance

- **Core engine:** Reusable intelligence layer for domain filtering, reputation, URL heuristics, caching, policy, classification, persistence, and diagnostics. It is independent of the UI and integration surfaces.
- **Windows Service:** Hosts the core engine and provides always-running, system-wide protection. It is the decision authority.
- **Dashboard:** Provides settings, statistics, classification history, allow/block and reputation management, and diagnostic tools.
- **Browser extension:** Provides page-level visibility through DOM, metadata, text, and image inspection. It is an observation layer, not the source of truth, and the service makes decisions.

## Development Methodology

Watchman is developed using [Behaviour Contract Driven Development (BCDD)](https://github.com/adadevoh/bcdd/blob/main/bcdd.md). Product behavior is defined through design documents and behavior contracts before implementation begins.

See:

- `docs/Watchman.design.md`
- `docs/bc/`

## License

TBD
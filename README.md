# Watchman

> "So you, son of man, I have made you a watchman..." — Ezekiel 33:7

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
- spdlog
- nlohmann/json

### AI & Computer Vision
- ONNX Runtime
- OpenCV

### Dashboard
- C#
- .NET

### Browser Integration
- TypeScript
- Chrome Extension APIs
- Edge Extension APIs

## Development Methodology

Watchman is developed using [Behaviour Contract Driven Development (BCDD)](https://github.com/adadevoh/bcdd/blob/main/bcdd.md). Product behavior is defined through design documents and behavior contracts before implementation begins.

See:

- `docs/Watchman.design.md`
- `docs/bc/`

## License

TBD
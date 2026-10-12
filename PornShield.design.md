# PornShield.design.md

# PornShield - Architecture & Development Roadmap ([BCDD](https://github.com/adadevoh/bcdd/blob/main/bcdd.md))

## Product Vision

PornShield is a local-first, system-wide Windows content filtering platform whose initial purpose is to help users prevent access to pornographic content across browsers and desktop applications.

The product is built using [Behaviour Contract Driven Development (BCDD)](https://github.com/adadevoh/bcdd/blob/main/bcdd.md). The design document defines the product, behaviour catalogue, guardrails, architecture, deployment strategy, and iteration plan. Observable behaviour is described in Behaviour Contracts under `bc/`.

---

# Product Scope

PornShield will evolve through three iterations:

1. Domain and reputation-based blocking
2. AI-assisted image classification
3. Browser-aware content inspection

The system must always prefer the cheapest successful classification path.

The image classifier is a last resort.

---

# Product Invariants

- The system is deny-by-confidence, not deny-by-guess.
- Known decisions must be cached.
- Classification decisions must be explainable.
- A cache hit must always be cheaper than reclassification.
- User overrides always take precedence over automatic classification.
- System-wide protection is the primary product.
- Browser integrations are enhancements, not dependencies.
- Classification logic must remain reusable regardless of integration surface.

---

# Locked Technology Stack

## Core Service

- C++20
- Windows Service
- SQLite
- spdlog
- nlohmann/json
- ONNX Runtime (Iteration 2)
- OpenCV (Iteration 2)

## Dashboard

- C#
- .NET

## Browser Extension (Iteration 3)

- TypeScript
- Chrome Extension APIs
- Edge Extension APIs

---

# High-Level Architecture

```text
+-------------------------+
| Dashboard (.NET)        |
+------------+------------+
             |
             v

+-------------------------+
| PornShield Service      |
| C++                     |
+-------------------------+
| Domain Engine           |
| Reputation Engine       |
| Cache Manager           |
| Rule Engine             |
| SQLite Repository       |
+------------+------------+
             |
             v

+-------------------------+
| Network Observation     |
| DNS / URL Visibility    |
+------------+------------+
             |
             v
          Internet
```

---



# Architectural Diagrams

## System Context Diagram

```text
+------------------+
|      User        |
+--------+---------+
         |
         v
+------------------+
|  Dashboard UI    |
|   (.NET/C#)      |
+--------+---------+
         |
         v
+------------------+
| PornShield       |
| Service (C++)    |
+--------+---------+
         |
         v
+------------------+
| Network Layer    |
| DNS / URLs       |
+--------+---------+
         |
         v
      Internet
```

## Iteration 1 Decision Pipeline

```text
Request
   |
   v
Known Bad Cache?
   |yes
   +----> BLOCK
   |
   no
   v
Known Good Cache?
   |yes
   +----> ALLOW
   |
   no
   v
Blacklist Match?
   |yes
   +----> BLOCK
   |
   no
   v
Reputation Engine
   |
   v
URL Heuristics
   |
   v
Decision
```

## Iteration 2 Classification Pipeline

```text
Request
   |
   v
Cache
   |
   v
Domain Rules
   |
   v
Reputation
   |
   v
URL Heuristics
   |
   v
Image Sampling
   |
   v
ONNX Classifier
   |
   v
Final Decision
```

## Iteration 3 Browser Integration

```text
+------------------+
| Browser Extension|
|  TypeScript      |
+--------+---------+
         |
         | metadata
         | DOM
         | images
         v
+------------------+
| PornShield       |
| Service (C++)    |
+--------+---------+
         |
         v
+------------------+
| Decision Engine  |
+--------+---------+
         |
         v
 Allow / Block
```


# Behaviour Catalogue

## Iteration 1

### BH-DOMAIN-FILTER

System blocks known adult domains.

BC:

```text
bc/domain-filter/domain-filter.bc.md
```

Status:

```text
Planned
```

### BH-REPUTATION

System stores and applies reputation information to domains.

BC:

```text
bc/reputation/reputation.bc.md
```

Status:

```text
Planned
```

### BH-URL-HEURISTICS

System evaluates unknown URLs using configurable heuristics.

BC:

```text
bc/url-heuristics/url-heuristics.bc.md
```

Status:

```text
Planned
```

### BH-CACHE

System caches known-good and known-bad decisions.

BC:

```text
bc/cache/cache.bc.md
```

Status:

```text
Planned
```

---

## Iteration 2

### BH-IMAGE-CLASSIFICATION

System classifies visual content on unknown sites.

BC:

```text
bc/image-classification/image-classification.bc.md
```

Status:

```text
Planned
```

---

## Iteration 3

### BH-BROWSER-INTEGRATION

System gains browser-level visibility and filtering.

BC:

```text
bc/browser-integration/browser-integration.bc.md
```

Status:

```text
Planned
```

---

# Iteration Plan

## Iteration 1

### Goal

Deliver a functional system-wide blocker that requires no AI.

### What Users Gain

Users can install PornShield and immediately receive protection against:

- Known adult domains
- Known adult DNS targets
- Suspicious adult URLs

Coverage includes:

- Chrome
- Edge
- Firefox
- Reddit desktop app
- Discord web requests
- Electron applications

### What Must Be Implemented

#### Domain Engine

Responsibilities:

- Block known domains
- Match wildcard domains
- Support user-maintained allow/block lists

Examples:

```text
*.example-adult-site.com
*.adult-cdn.net
```

#### Reputation Engine

Responsibilities:

- Persist domain reputation
- Track confidence
- Record first/last-seen data

Example:

```text
adultcams.example
score = 95
classification = adult
```

#### URL Heuristics Engine

Responsibilities:

- Analyze domain names
- Analyze paths
- Analyze query strings
- Calculate risk scores

Example:

```text
free-sex-videos.com/live-cams
```

#### Cache Manager

Responsibilities:

- Known-good cache
- Known-bad cache
- TTL management

Goal:

```text
O(1) lookup before reclassification
```

#### SQLite Repository

Stores:

- Domain decisions
- Reputation data
- User overrides
- Configuration
- Statistics

### Success Metric

Majority of blocking decisions occur without expensive analysis.

---

## Iteration 2

### Goal

Detect pornography on domains that are not already known.

### What Users Gain

Users are protected from:

- Newly created domains
- Unknown image hosts
- Previously unseen adult websites

### What Must Be Implemented

#### Image Classification Engine

Responsibilities:

- Sample images
- Preprocess images
- Run ONNX models
- Produce confidence values

Input:

```text
Image
```

Output:

```text
Adult probability
```

#### Classification Pipeline

```text
Request
  ↓
Cache
  ↓
Domain Rules
  ↓
Reputation
  ↓
URL Heuristics
  ↓
Image Classification
  ↓
Decision
```

### Success Metric

Previously unseen adult content can be identified with high confidence.

---

## Iteration 3

### Goal

Add browser-level content awareness.

### What Users Gain

The system can distinguish between:

```text
reddit.com/r/programming
```

and

```text
reddit.com/r/nsfw
```

without blocking all of Reddit.

### What Must Be Implemented

#### Browser Extension

Responsibilities:

- Read DOM content
- Read page metadata
- Inspect visible images
- Replace pages with block screens

#### Service Integration

Extension becomes an observation layer.

The C++ service remains the decision engine.

Flow:

```text
Browser Page
  ↓
Extension Analysis
  ↓
PornShield Service
  ↓
Decision
  ↓
Render/Block
```

### Success Metric

User-generated-content platforms can be filtered at page level rather than domain level.

---

# Deployment Strategy

## Development

Local workstation.

## Packaging

Windows installer.

## Runtime

Windows Service started automatically.

## Shipped Definition

A behaviour is shipped only when:

- Its BC exists.
- Its BC is marked done.
- No unimplemented signal remains.
- Associated tests pass.
- Design document and BC agree.

---

# Unimplemented Signal

All scaffolded but unfinished behaviour must return:

```text
NOT_IMPLEMENTED
```

or the platform-specific equivalent.

The signal must never be mistaken for a successful classification decision.

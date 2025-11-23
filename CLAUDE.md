# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Language learning web application with flashcards, quizzes, sentence rewriting exercises, text-to-speech, and progress tracking.

## Tech Stack

- **Frontend:** Vue.js 3 + TypeScript + Vite
- **Backend:** Native Node.js `http` module (no Express)
- **Database:** PostgreSQL with `pg` driver (no ORM)
- **Testing:** Vitest (frontend), Node test runner (backend)

## Architecture

Clean/Onion Architecture with DDD principles. High cohesion within modules, low coupling between modules.

### Server Structure

```
server/src/
├── shared/
│   ├── interfaces/         # Cross-cutting abstractions
│   ├── events/             # Event bus (Mediator pattern)
│   └── errors/
├── modules/{feature}/
│   ├── domain/
│   │   ├── entities/       # Business logic + data (Information Expert)
│   │   ├── value-objects/  # Immutable types
│   │   └── repositories/   # Interfaces only
│   ├── application/
│   │   ├── use-cases/      # Business operations
│   │   ├── dto/            # Input/Output DTOs
│   │   └── strategies/     # Algorithm interfaces
│   └── infrastructure/
│       ├── persistence/    # PostgreSQL repositories + mappers
│       ├── http/           # Controllers + request/response DTOs
│       └── adapters/       # External service adapters
├── container/              # DI composition root
└── main.ts
```

### Client Structure

```
client/src/
├── domain/                 # Frontend types
├── application/            # Composables, state
├── infrastructure/         # API clients
└── presentation/           # Vue components
```

## Design Principles

- **DI:** Constructor injection, all dependencies on interfaces
- **Repository Pattern:** Interface in domain, implementation in infrastructure
- **DTOs:** HTTP layer → Application layer → Domain entities → DB models
- **Events:** Domain events via Mediator for cross-module communication
- **Race Conditions:** Optimistic locking (version columns), transactions, idempotency keys

## Modules

| Module | Purpose |
|--------|---------|
| auth | User identity, JWT tokens |
| vocabulary | Languages, words, sentences |
| flashcard | Spaced repetition (SM-2) |
| exercise | Sentence rewriting practice |
| quiz | Assessment and scoring |
| progress | Learning analytics |

## Commands

```bash
# Install dependencies
npm install

# Development
npm run dev:client          # Start Vue dev server
npm run dev:server          # Start Node server

# Testing
npm test                    # Run all tests
npm run test:client         # Frontend tests (Vitest)
npm run test:server         # Backend tests (Node test runner)
npm test -- --filter="name" # Run single test

# Database
npm run db:migrate          # Run migrations
npm run db:seed             # Seed test data

# Build
npm run build               # Build both client and server
```

## Testing Approach

- Unit test all use cases with mocked repositories
- Test domain entities and value objects directly
- Integration tests with real database for repositories
- Component tests for Vue components

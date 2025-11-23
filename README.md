# Language Trainer

A web application for learning foreign languages through flashcards, quizzes, sentence rewriting exercises, and spaced repetition.

## Summary

| Aspect | Description |
|--------|-------------|
| **Purpose** | Help users learn foreign languages |
| **Platform** | Web application |
| **Frontend** | Vue.js 3 + TypeScript + Vite |
| **Backend** | Native Node.js `http` module |
| **Database** | PostgreSQL with `pg` driver |
| **Architecture** | Clean/Onion with DDD, High Cohesion, Low Coupling |
| **Testing** | Vitest (frontend), Node test runner (backend) |
| **Dependencies** | Minimal - no Express, no ORM |

## Features

- **Flashcards** - Spaced repetition (SM-2 algorithm)
- **Sentence Rewriting** - Practice writing in target language
- **Quizzes** - Multiple choice, fill-in-the-blank
- **Text-to-Speech** - Web Speech API
- **Progress Tracking** - Statistics, streaks, analytics

## Design Principles

- **Low Coupling** - Modules depend on abstractions, not implementations
- **High Cohesion** - Each module has single purpose, internal elements work together
- **DI/IoC** - Constructor injection, composition root
- **Repository Pattern** - Interface in domain, implementation in infrastructure
- **DTOs** - Separate data structures per layer
- **Patterns** - Mediator, Observer, Strategy, Adapter, Proxy, Facade, Composite

---

## Implementation Plan

### Stage 1: Foundation

- Initialize monorepo structure
- Set up Vue.js 3 + TypeScript with Vite
- Set up Node.js with native `http` server and custom router
- Implement DI container
- Implement Event bus (Mediator)
- Configure PostgreSQL with `pg` driver
- Implement Unit of Work for transactions
- Set up test runners
- **Unit tests:** container, event bus, database, router

### Stage 2: Auth Module

**Cohesive Purpose:** User identity and access

- **Domain:** User entity (Information Expert), Email/Password VOs
- **Interfaces:** IUserRepository, IPasswordHasher, ITokenService
- **Use cases:** RegisterUser, LoginUser, RefreshToken, LogoutUser
- **Infrastructure:** PostgresUserRepository, JwtService, AuthController
- **Facade:** AuthFacade
- **Race conditions:** Unique email constraint, token refresh locking
- Vue auth pages (login, register)
- **Unit tests:** all use cases with mocked repositories
- **Docs:** Swagger + JSDoc

### Stage 3: Vocabulary Module

**Cohesive Purpose:** Content management

- **Domain:** Language, Vocabulary, Sentence entities
- **Interfaces:** IVocabularyRepository, ISentenceRepository
- **Use cases:** CRUD operations for content
- **Infrastructure:** PostgreSQL repositories, mappers, controllers
- **Facade:** ContentFacade
- Admin UI for content management
- **Unit tests:** use cases with mocks
- **Docs:** Swagger + JSDoc

### Stage 4: Flashcard Module

**Cohesive Purpose:** Spaced repetition learning

- **Domain:** Flashcard entity (with version), ReviewSchedule VO
- **Interfaces:** IFlashcardRepository, ISchedulingStrategy
- **Strategy:** SM2Strategy implements ISchedulingStrategy
- **Use cases:** PracticeFlashcard, ScheduleReview
- **Events:** FlashcardReviewedEvent → Progress (via Mediator)
- **Race conditions:** Optimistic locking on review updates
- Vue flashcard component with CSS animations
- **Unit tests:** SM-2 algorithm, concurrency scenarios
- **Docs:** Swagger + JSDoc

### Stage 5: Exercise Module

**Cohesive Purpose:** Sentence practice

- **Domain:** Exercise entity, Score VO, Answer
- **Interfaces:** IExerciseRepository, IAnswerValidator
- **Strategies:** ExactMatchValidator, FuzzyMatchValidator (Levenshtein)
- **Composite:** Different validators share IAnswerValidator interface
- **Use cases:** GetExercise, ValidateAnswer
- **Race conditions:** Idempotent answer submission
- Vue exercise component
- **Unit tests:** validation strategies, fuzzy matching
- **Docs:** Swagger + JSDoc

### Stage 6: Quiz Module

**Cohesive Purpose:** Assessment

- **Domain:** Quiz, QuizResult entities, Question types (Composite)
- **Interfaces:** IQuizRepository, IQuizResultRepository, IDifficultyStrategy
- **Use cases:** GenerateQuiz, SubmitQuiz, AdjustDifficulty
- **Events:** QuizCompletedEvent → Progress (Mediator, Low Coupling)
- **Race conditions:** Idempotency keys, transaction for scoring
- Vue quiz components (MCQ, fill-blank)
- **Unit tests:** generation, scoring, idempotency
- **Docs:** Swagger + JSDoc

### Stage 7: TTS Module

**Cohesive Purpose:** Audio pronunciation

- **Interface:** ITtsService
- **Adapter:** WebSpeechAdapter
- **Proxy:** CachedTtsProxy (optional)
- Vue TTS composable
- **Unit tests:** adapter with mock SpeechSynthesis
- **Docs:** JSDoc

### Stage 8: Progress Module

**Cohesive Purpose:** Learning analytics

- **Domain:** Progress entity, Streak VO, Statistics
- **Interfaces:** IProgressRepository, IStatisticsCalculator
- **Event handlers:** Subscribe to QuizCompletedEvent, FlashcardReviewedEvent
- **Use cases:** GetStatistics, TrackProgress
- **Facade:** StatsFacade
- **Race conditions:** Atomic streak updates, transaction for aggregation
- Dashboard with Canvas/SVG charts
- **Unit tests:** statistics calculations, event handlers
- **Docs:** Swagger + JSDoc

### Stage 9: Polish & Final

- Responsive CSS design
- Global error handling (including concurrency errors)
- Performance optimization
- **Integration tests with concurrent scenarios**
- **>80% unit test coverage**
- Complete API documentation review
- Finalize README with deployment guide

---

## Quick Start

### Prerequisites

- Node.js 20+
- PostgreSQL 15+

### Installation

```bash
npm install
cp .env.example .env
npm run db:migrate
npm run db:seed
```

### Development

```bash
npm run dev:client    # Vue (port 5173)
npm run dev:server    # Node (port 3000)
```

### Testing

```bash
npm test              # All tests
npm run test:client   # Frontend
npm run test:server   # Backend
```

### Build

```bash
npm run build
```

## Project Structure

```
├── client/           # Vue.js frontend
├── server/           # Node.js backend
├── database/         # Migrations and seeds
└── docs/             # API documentation
```

## License

MIT

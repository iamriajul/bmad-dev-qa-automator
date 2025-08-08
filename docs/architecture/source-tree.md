# Source Tree

Based on the monorepo structure, TypeScript/Node.js stack, and NPX distribution requirements, here's the project folder structure:

```plaintext
bmad-dev-qa-automator/
├── src/
│   ├── index.ts                       # CLI entry point
│   ├── commands/
│   │   ├── start.ts                   # Main automation command
│   │   ├── status.ts                  # Check current state
│   │   └── stop.ts                    # Graceful shutdown
│   ├── core/
│   │   ├── workflow-engine.ts         # Main orchestration logic
│   │   ├── state-processor.ts         # stories.json management
│   │   ├── dependency-resolver.ts     # Story dependency graph
│   │   └── story-manager.ts           # Story file operations
│   ├── agents/
│   │   ├── agent-orchestrator.ts      # Agent routing and retry
│   │   ├── claude-wrapper.ts          # Claude SDK abstraction
│   │   ├── prompt-manager.ts          # Agent prompt templates
│   │   └── response-parser.ts         # Parse agent responses
│   ├── models/
│   │   ├── story.ts                   # Story interfaces
│   │   ├── agent.ts                   # Agent types
│   │   ├── state.ts                   # State interfaces
│   │   └── config.ts                  # Configuration types
│   ├── utils/
│   │   ├── file-system.ts            # File operations
│   │   ├── logger.ts                  # Winston logger setup
│   │   ├── circuit-breaker.ts        # Retry logic
│   │   └── validators.ts              # Zod schemas
│   └── config/
│       ├── default.ts                 # Default configuration
│       └── prompts/
│           ├── sm-prompts.ts          # Story Manager templates
│           ├── dev-prompts.ts         # Developer templates
│           └── qa-prompts.ts          # QA templates
├── tests/
│   ├── unit/
│   │   ├── core/
│   │   │   ├── workflow-engine.test.ts
│   │   │   ├── state-processor.test.ts
│   │   │   ├── dependency-resolver.test.ts
│   │   │   └── story-manager.test.ts
│   │   ├── agents/
│   │   │   ├── agent-orchestrator.test.ts
│   │   │   ├── prompt-manager.test.ts
│   │   │   └── response-parser.test.ts
│   │   └── utils/
│   │       ├── file-system.test.ts
│   │       └── circuit-breaker.test.ts
│   ├── integration/
│   │   ├── workflow.test.ts           # End-to-end workflow tests
│   │   ├── agent-handoff.test.ts      # Agent transition tests
│   │   └── state-persistence.test.ts  # stories.json tests
│   ├── fixtures/
│   │   ├── stories/
│   │   │   ├── valid-story.md         # Test story files
│   │   │   ├── invalid-story.md
│   │   │   └── complex-deps.md
│   │   ├── responses/
│   │   │   ├── sm-response.txt        # Mock agent responses
│   │   │   ├── dev-response.txt
│   │   │   └── qa-response.txt
│   │   └── stories.json               # Test state file
│   └── test-utils/
│       ├── mock-claude.ts             # Claude SDK mocks
│       ├── test-builders.ts           # Test data builders
│       └── assertions.ts              # Custom assertions
├── docs/
│   ├── prd.md                         # Product Requirements
│   ├── brief.md                       # Project Brief
│   ├── architecture.md                # This document
│   └── stories/                       # Story files (created by SM)
│       ├── 1.1.project-setup.md
│       └── 1.2.story-discovery.md
├── scripts/
│   ├── build.ts                       # Build for NPX
│   ├── release.ts                     # NPM publish script
│   └── dev.ts                         # Development runner
├── dist/                              # Built output (gitignored)
│   ├── index.js                       # Bundled CLI
│   └── index.d.ts                     # Type definitions
├── logs/                              # Runtime logs (gitignored)
│   └── automator.log
├── .github/
│   └── workflows/
│       └── ci.yml                     # GitHub Actions CI
├── package.json                       # NPM package definition
├── tsconfig.json                      # TypeScript configuration
├── tsconfig.build.json               # Build-specific TS config
├── jest.config.js                     # Jest configuration
├── .eslintrc.js                      # ESLint rules
├── .prettierrc                       # Prettier formatting
├── .gitignore                         # Git ignore rules
├── .npmignore                         # NPM publish ignore
├── .env.example                      # Environment variables template
├── .bmad-automator.json              # Default configuration
├── stories.json                       # State file (gitignored)
└── README.md                          # Project documentation
```

**NPX Distribution Structure:**
```json
{
  "name": "bmad-automator",
  "version": "1.0.0",
  "bin": {
    "bmad-automator": "./dist/index.js"
  },
  "files": [
    "dist",
    "README.md"
  ]
}
```

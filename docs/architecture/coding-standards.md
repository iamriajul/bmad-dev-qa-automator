# Coding Standards

These standards are MANDATORY for AI agents generating code for this project.

## Core Standards

- **Languages & Runtimes:** TypeScript 5.9.2, Node.js 22.11.0 LTS
- **Style & Linting:** ESLint 9.17.0 with standard config, Prettier 3.4.2 for formatting
- **Test Organization:** Mirror structure - `src/module.ts` → `tests/unit/module.test.ts`

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Files | kebab-case | `story-manager.ts` |
| Classes | PascalCase | `StoryManager` |
| Interfaces | PascalCase with I prefix | `IStoryManager` |
| Functions | camelCase | `parseStoryFile()` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES` |
| Test files | .test.ts suffix | `story-manager.test.ts` |

## Critical Rules

- **Never use console.log:** Always use logger instance - `this.logger.info()` or `logger.error()`
- **All async functions must have error handling:** Wrap in try/catch or use .catch()
- **File operations must use atomic writes:** Use `writeFileAtomic()` from utils, never direct fs.writeFile
- **Agent prompts must include response format:** Always append response format requirements to prompts
- **Tests must be written BEFORE implementation:** TDD is mandatory - test file must exist with failing tests
- **All public methods need JSDoc comments:** Include @param, @returns, @throws
- **Status transitions only through StateProcessor:** Never modify story status directly
- **Dependencies must be validated:** Check for circular dependencies before saving
- **Parse errors need fallback strategies:** Always have at least regex fallback for agent responses

## TypeScript Specifics

- **Strict mode required:** tsconfig must have `"strict": true`
- **No any types:** Use `unknown` and narrow types, or define proper interfaces
- **Prefer const assertions:** Use `as const` for literal types
- **Explicit return types:** All public methods must declare return types
- **Null checks required:** Use optional chaining `?.` and nullish coalescing `??`

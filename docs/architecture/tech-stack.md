# Tech Stack

This is the DEFINITIVE technology selection section. These choices will guide all implementation decisions throughout the project.

## Cloud Infrastructure

- **Provider:** N/A - This is a client-side CLI tool
- **Key Services:** None - Runs locally on developer machines
- **Deployment Regions:** NPM Registry (global distribution)

## Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| **Language** | TypeScript | 5.9.2 | Primary development language | Latest stable (Jan 2025), strong typing for reliability, Go port coming in v7 |
| **Runtime** | Node.js | 22.11.0 | JavaScript runtime | Latest LTS (Active until Oct 2025), native fetch, stable performance |
| **Package Manager** | pnpm | 9.15.0 | Monorepo package management | Excellent workspace support, fast installs, content-addressable storage |
| **AI SDK** | @anthropic-ai/sdk | 0.57.0 | Agent communication | Official SDK, better error handling than CLI, streaming support |
| **CLI Framework** | Commander.js | 14.0.0 | Command-line interface | Latest version, requires Node 20+, excellent TypeScript support |
| **State Validation** | Zod | 4.0.14 | Runtime validation | Latest v4 (57% smaller bundle), 10x faster compilation, type-safe schemas |
| **File Watching** | Chokidar | 3.6.0 | Cross-platform file monitoring | Reliable, handles edge cases, good performance |
| **Logging** | Winston | 3.17.0 | Structured logging | Latest stable, flexible transports, JSON format, log rotation |
| **Testing Framework** | Jest | 30.0.5 | Unit and integration testing | Latest v30, requires TypeScript 5.4+, faster & leaner |
| **Test Utilities** | ts-jest | 29.4.0 | TypeScript support for Jest | Note: May need update for Jest 30 compatibility |
| **Mocking** | jest-mock-extended | 3.0.7 | Type-safe mocking | Better TypeScript mocking experience |
| **Build Tool** | ESBuild | 0.25.8 | Bundling for NPX | Latest version, 10-100x faster than alternatives |
| **Linting** | ESLint | 9.17.0 | Code quality enforcement | Latest major version with flat config support |
| **Formatting** | Prettier | 3.4.2 | Code formatting | Latest stable, consistent style |
| **Git Hooks** | Husky | 9.2.0 | Pre-commit hooks | Latest version, ensures tests pass before commit |
| **Commit Linting** | Commitlint | 19.6.0 | Commit message standards | Enforces conventional commits |
| **Schema Types** | type-fest | 4.36.0 | Advanced TypeScript types | Utility types for complex scenarios |
| **Date Handling** | date-fns | 3.6.0 | Date manipulation | Lightweight, functional, tree-shakeable |
| **Process Management** | Execa | 9.5.2 | Child process execution | Latest version, Promise-based, better than native |
| **Type Definitions** | @types/jest | 30.0.0 | Jest TypeScript types | Matches Jest 30 for full type safety |

**⚠️ IMPORTANT: This technology stack is the single source of truth for the entire project. All implementation must use these exact versions and technologies.**

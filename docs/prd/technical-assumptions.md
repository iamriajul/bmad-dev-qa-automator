# Technical Assumptions

## Repository Structure: Monorepo
The entire BMad automation tool will be contained in a single repository, making it easy to distribute as an NPX package and maintain consistent versioning.

## Service Architecture
**Monolithic CLI Application** - A single Node.js process that orchestrates all agent interactions. This is appropriate for the MVP scope where we're managing sequential story processing. The event-driven architecture within the monolith will allow future expansion to parallel processing.

## Testing Requirements
**Unit + Integration Testing** - Unit tests for core logic (state machine, dependency resolver, story parser) and integration tests for Claude SDK interactions. Manual testing convenience through CLI flags like `--dry-run` to preview actions without executing.

## Additional Technical Assumptions and Requests
- **Claude TypeScript SDK** will be used instead of CLI for better error handling and streaming responses
- **Node.js 18+** required for native fetch and modern JavaScript features
- **Chokidar** for cross-platform file watching of the stories directory
- **Zod** for runtime validation of stories.json schema
- **Winston** for structured logging with configurable log levels
- **Environment Variables** for ANTHROPIC_API_KEY configuration
- **No GUI** for MVP - pure CLI tool with console output
- **Sequential Processing** only - no parallel story/epic execution, process one epic at a time
- **File-based State** - stories.json as single source of truth, no database required

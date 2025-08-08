# BMad CLI Automation Tool Product Requirements Document (PRD)

## Goals and Background Context

### Goals
- Enable 24/7 autonomous development by orchestrating SM-Dev-QA agent collaboration without human intervention
- Reduce agent orchestration time from 3+ hours/day to <10 minutes/day for BMad Method teams
- Achieve 4x faster feature delivery through overnight AI development cycles
- Provide clear audit trails and morning summaries of all AI agent actions
- Support dependency-aware story execution based on SM-generated dependency graphs
- Exit gracefully when SM determines no more stories are needed to complete the epic

### Background Context

Development teams using BMad Method have three capable AI agents (Story Manager, Developer, and QA) that currently sit idle for 76% of available time due to manual orchestration requirements. This CLI automation tool transforms these idle agents into a 24/7 autonomous development team by monitoring story status, launching appropriate Claude sessions, and managing context handoffs between agents. With unlimited Claude subscriptions becoming common and story-isolated contexts maintaining agent focus, the primary barrier to continuous development is the manual overhead of launching sessions and tracking progress. This tool addresses that gap, enabling teams to wake up to completed features instead of yesterday's backlog.

### Change Log

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2024-01-26 | 1.0 | Initial PRD creation | John (PM) |
| 2024-01-26 | 1.1 | Updated with sequential processing and enhanced TDD requirements | John (PM) |

## Requirements

### Functional

- **FR1**: The system shall generate/update a `stories.json` file by asking SM agent to scan all story files in `docs/stories` directory
- **FR2**: The `stories.json` shall contain an array of story objects with fields: name (filename), status, dependencies (array of story names), and signatures (audit trail)
- **FR3**: When `stories.json` exists, the system shall ask SM agent to identify new stories and update the JSON while maintaining array order
- **FR4**: The system shall process stories in priority order: Ready For Review → Dev → Approved → Draft
- **FR5**: Within each status category, stories shall be processed respecting dependency order from the JSON array
- **FR6**: For Draft stories, the system shall instruct QA agent: "Review the story ensuring it follows TDD approach with comprehensive test cases defined. Verify all acceptance criteria are testable. Change status to Approved if it passes your checks, otherwise keep it as Draft with your findings. Your signature is required for any changes."
- **FR7**: For Approved stories, the system shall instruct Dev agent: "Implement this story following strict TDD approach - write all tests first based on acceptance criteria, ensure tests fail, then implement code to make tests pass. Change status to Ready For Review when complete. Your signature is required."
- **FR8**: For Dev status stories, the system shall instruct Dev agent: "Continue TDD implementation - ensure all tests are written and passing. Change status to Ready For Review when complete. Your signature is required."
- **FR9**: For Ready For Review stories, the system shall instruct QA agent: "Review the implementation thoroughly - verify all tests exist, pass, and achieve 90%+ coverage. Run all tests and validate the implementation meets acceptance criteria. Take as much time as needed for comprehensive validation. Change status to Done if it passes, or back to Dev with specific findings. Your signature is required."
- **FR10**: Every agent handoff shall explicitly request: status change based on outcome AND signature addition
- **FR11**: The system shall parse story files after each agent action to extract: updated status, new signatures, and any findings
- **FR12**: The system shall use the last signature to determine next agent (e.g., Draft + QA signature → send to SM with QA's findings)
- **FR13**: When instructing SM about QA findings, the system shall say: "Review QA's findings and revise the story to ensure TDD approach with comprehensive test cases in acceptance criteria. Change status to Draft and add your signature."
- **FR14**: When no incomplete stories remain, the system shall ask SM: "Are more stories needed to complete any epic? Update stories.json and add your signature."
- **FR15**: The system shall exit gracefully when SM confirms no more stories needed for any epic

### Non Functional

- **NFR1**: The system shall process epics sequentially, one at a time, with no parallel execution
- **NFR2**: Quality assurance shall have unlimited time allocation - no timeout constraints on testing/validation cycles
- **NFR3**: The system shall enforce TDD approach - tests must exist before any implementation code
- **NFR4**: Test coverage shall maintain 90% minimum across all components
- **NFR5**: Agent responses must be parsed within 5 seconds of completion to maintain workflow momentum
- **NFR6**: The system shall use TypeScript with full type safety for story objects and state management
- **NFR7**: All agent interactions shall be logged with timestamps for audit trail
- **NFR8**: The system shall handle Claude API failures with exponential backoff retry (max 3 attempts)
- **NFR9**: Story file parsing must handle malformed YAML frontmatter gracefully
- **NFR10**: The system shall validate story dependencies don't create circular references
- **NFR11**: Memory usage shall remain under 512MB during normal operation
- **NFR12**: Agent signatures must include timestamp and agent identifier for traceability

## Technical Assumptions

### Repository Structure: Monorepo
The entire BMad automation tool will be contained in a single repository, making it easy to distribute as an NPX package and maintain consistent versioning.

### Service Architecture
**Monolithic CLI Application** - A single Node.js process that orchestrates all agent interactions. This is appropriate for the MVP scope where we're managing sequential story processing. The event-driven architecture within the monolith will allow future expansion to parallel processing.

### Testing Requirements
**Unit + Integration Testing** - Unit tests for core logic (state machine, dependency resolver, story parser) and integration tests for Claude SDK interactions. Manual testing convenience through CLI flags like `--dry-run` to preview actions without executing.

### Additional Technical Assumptions and Requests
- **Claude TypeScript SDK** will be used instead of CLI for better error handling and streaming responses
- **Node.js 18+** required for native fetch and modern JavaScript features
- **Chokidar** for cross-platform file watching of the stories directory
- **Zod** for runtime validation of stories.json schema
- **Winston** for structured logging with configurable log levels
- **Environment Variables** for ANTHROPIC_API_KEY configuration
- **No GUI** for MVP - pure CLI tool with console output
- **Sequential Processing** only - no parallel story/epic execution, process one epic at a time
- **File-based State** - stories.json as single source of truth, no database required

## Epic List

1. **Epic 1: Foundation & Core Orchestration Engine** - Establish project infrastructure with TDD, implement story discovery, and basic agent orchestration with SM-generated stories.json

2. **Epic 2: Agent Integration & State Management** - Implement Claude SDK integration, agent handoff logic with signatures, and story status state machine

3. **Epic 3: Workflow Automation & Recovery** - Build the complete automation cycles (SM-QA, Dev-QA), implement retry logic, and graceful error handling

## Epic 1: Foundation & Core Orchestration Engine

**Goal**: Establish the foundational infrastructure for the BMad automation tool using strict Test-Driven Development. Every acceptance criteria must have corresponding tests written BEFORE implementation.

### Story 1.1: Project Setup with Test Infrastructure

As a developer,  
I want to initialize the TypeScript project with comprehensive test infrastructure,  
so that every subsequent story can follow TDD practices.

#### Acceptance Criteria
1: Jest test framework configured with TypeScript support and coverage reporting
2: Test structure mirrors source structure: src/ → tests/ with identical paths
3: Pre-commit hooks run all tests and block commits if tests fail
4: Coverage threshold set to 90% minimum for all files
5: Test utilities created for mocking Claude SDK responses
6: Test fixtures directory with sample story files (valid, invalid, edge cases)
7: GitHub Actions CI pipeline runs tests on every push
8: npm test runs unit tests, npm run test:integration runs integration tests
9: Test documentation explains TDD workflow and test naming conventions

### Story 1.2: Story Discovery Test Suite and Implementation

As a developer,  
I want to implement story discovery using TDD with tests for all edge cases,  
so that the scanner is bulletproof from day one.

#### Acceptance Criteria
1: Write unit tests for story file discovery covering: empty directory, single file, 1000+ files, non-.md files, malformed YAML, missing frontmatter, concurrent modifications
2: Write tests for stories.json generation including: schema validation, empty arrays, order preservation, Unicode handling
3: Implement scanner only after all tests are written and failing
4: Performance tests verify 1000 stories scan under 5 seconds
5: Memory usage tests confirm under 100MB for large scans
6: Integration tests cover real file system operations
7: Test coverage for scanner must be 100%

### Story 1.3: Priority Queue Test Suite and Implementation

As a developer,  
I want to implement the priority queue with comprehensive test coverage,  
so that story processing order is guaranteed correct.

#### Acceptance Criteria
1: Unit tests cover: empty queue, single story, status priority order, dependency respect, circular detection, missing dependencies, invalid status
2: Integration tests cover: deep dependency chains, mixed priorities, all blocked scenarios, queue persistence
3: Property-based tests ensure queue determinism and correct ordering
4: Performance benchmarks confirm O(n log n) complexity
5: Queue builds 1000 stories in under 100ms
6: Test coverage for queue must be 100%
7: Tests document expected behavior for all edge cases

### Story 1.4: Test Harness for Agent Mocking

As a developer,  
I want to create a comprehensive test harness for mocking agent interactions,  
so that we can test the full system without calling real APIs.

#### Acceptance Criteria
1: Mock Claude SDK client with configurable responses
2: Mock agent response generator for SM, Dev, QA personas
3: Test data builders for creating story objects
4: Assertion helpers for verifying agent calls
5: Scenario runners for end-to-end test flows
6: Performance profiler integrated into test runs
7: Test report generator with failure analysis

## Epic 2: Agent Integration & State Management

**Goal**: Implement the Claude SDK integration and agent communication layer with comprehensive state management. All agent interactions must be predictable, traceable, and thoroughly tested.

### Story 2.1: Claude SDK Integration with TDD

As a developer,  
I want to integrate the Claude TypeScript SDK with full test coverage,  
so that agent communications are reliable and mockable.

#### Acceptance Criteria
1: Write tests for SDK initialization: valid key, invalid key, missing key, network errors
2: Write tests for message sending: successful response, timeout, rate limit, API errors
3: Write tests for response streaming: partial responses, complete responses, stream interruption
4: Implement thin wrapper around Claude SDK only after tests pass
5: Retry logic tests: exponential backoff, max retries, retry on specific errors only
6: Context management tests: conversation history, context window limits
7: All SDK calls must be mockable for other test suites

### Story 2.2: Agent Prompt Templates and Testing

As a developer,  
I want to create tested prompt templates for each agent type,  
so that agent instructions are consistent and produce parseable outputs.

#### Acceptance Criteria
1: Create parameterized prompt templates for SM, Dev, QA agents
2: Test templates include all required instructions (status change, signature)
3: Templates tested with mock responses to ensure parseability
4: Version control for prompts with migration strategy
5: Prompt validation ensures required parameters are provided
6: Templates support dry-run mode for testing
7: Documentation of expected agent response formats

### Story 2.3: Story Status State Machine

As a developer,  
I want to implement a state machine for story status transitions,  
so that only valid status changes are allowed.

#### Acceptance Criteria
1: Write tests for all valid transitions: Draft→Approved, Approved→Dev, Dev→Ready For Review, Ready For Review→Done
2: Write tests for invalid transitions: Draft→Done, Done→Draft, etc.
3: Write tests for conditional transitions: Ready For Review→Dev (with QA findings)
4: State machine enforces signature requirements for transitions
5: State persistence tests: save/load state correctly
6: Concurrent state change prevention
7: State history tracking for audit trail

### Story 2.4: Agent Response Parser

As a developer,  
I want to implement a robust parser for agent responses,  
so that we can extract status, signatures, and findings reliably.

#### Acceptance Criteria
1: Parser tests for well-formed responses: status changes, signatures, findings
2: Parser tests for malformed responses: missing fields, wrong format, unexpected content
3: Parser extracts status with fallback to previous status
4: Parser extracts signatures with timestamp validation
5: Parser extracts QA findings for SM revision
6: Parser handles multi-line findings and code blocks
7: Parser errors are descriptive for debugging

## Epic 3: Workflow Automation & Recovery

**Goal**: Build the complete automation workflows that orchestrate agents through story lifecycles, with robust error handling and recovery mechanisms.

### Story 3.1: SM-QA Approval Workflow

As a developer,  
I want to implement the SM-QA story approval workflow,  
so that Draft stories can be automatically reviewed and approved.

#### Acceptance Criteria
1: Test workflow for simple approval: Draft → QA Review → Approved
2: Test workflow for rejection: Draft → QA Review → Draft (with findings) → SM Revision
3: Test infinite loop prevention: Max 3 revision cycles before escalation
4: Workflow preserves all signatures and findings in story file
5: stories.json updates after each state change
6: Workflow handles agent timeout gracefully
7: End-to-end test with mock agents completes in under 30 seconds

### Story 3.2: Dev-QA Implementation Workflow

As a developer,  
I want to implement the Dev-QA implementation workflow,  
so that approved stories are automatically developed and reviewed.

#### Acceptance Criteria
1: Test workflow: Approved → Dev Implementation → Ready For Review → QA Review → Done
2: Test workflow with fixes: Ready For Review → QA finds issues → Dev → Ready For Review
3: Test abandoned implementation detection: Dev status for >2 hours without progress
4: Circuit breaker after 5 Dev-QA cycles
5: Implementation files tracked in stories.json
6: Workflow handles large code generation responses
7: End-to-end test with mock agents verifies complete flow

### Story 3.3: Orchestration Engine

As a developer,  
I want to implement the main orchestration engine,  
so that all workflows run automatically based on story states.

#### Acceptance Criteria
1: Engine starts with stories.json scan or generation
2: Engine processes stories in priority order
3: Engine invokes appropriate workflow based on story status
4: Engine handles workflow failures with retry logic
5: Engine logs all actions with timestamps
6: Engine supports graceful shutdown (Ctrl+C)
7: Engine provides dry-run mode for testing
8: Integration test runs 10 story automation cycle

### Story 3.4: Epic Completion Detection

As a developer,  
I want the system to detect when all epics are complete,  
so that it can exit gracefully without manual intervention.

#### Acceptance Criteria
1: When no incomplete stories exist, system asks SM about remaining work
2: SM response parsed for epic completion signal
3: System updates stories.json with completion status
4: System generates final summary report
5: System exits with success code when all epics complete
6: Test covers multiple epic scenarios
7: Completion detection works after failures/retries

### Story 3.5: Monitoring and Observability

As a developer,  
I want comprehensive logging and monitoring,  
so that I can review overnight automation results.

#### Acceptance Criteria
1: Structured JSON logs with log levels (debug, info, warn, error)
2: Separate log files per run with timestamp
3: Summary report shows: stories completed, time taken, errors encountered
4: Agent conversation logs preserved for debugging
5: Performance metrics: API calls, response times, token usage
6: Morning dashboard generator creates HTML summary
7: Logs are automatically rotated after 7 days
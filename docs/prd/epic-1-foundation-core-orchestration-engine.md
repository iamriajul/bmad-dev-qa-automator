# Epic 1: Foundation & Core Orchestration Engine

**Goal**: Establish the foundational infrastructure for the BMad automation tool using strict Test-Driven Development. Every acceptance criteria must have corresponding tests written BEFORE implementation.

## Story 1.1: Project Setup with Test Infrastructure

As a developer,  
I want to initialize the TypeScript project with comprehensive test infrastructure,  
so that every subsequent story can follow TDD practices.

### Acceptance Criteria
1: Jest test framework configured with TypeScript support and coverage reporting
2: Test structure mirrors source structure: src/ → tests/ with identical paths
3: Pre-commit hooks run all tests and block commits if tests fail
4: Coverage threshold set to 90% minimum for all files
5: Test utilities created for mocking Claude SDK responses
6: Test fixtures directory with sample story files (valid, invalid, edge cases)
7: GitHub Actions CI pipeline runs tests on every push
8: npm test runs unit tests, npm run test:integration runs integration tests
9: Test documentation explains TDD workflow and test naming conventions

## Story 1.2: Story Discovery Test Suite and Implementation

As a developer,  
I want to implement story discovery using TDD with tests for all edge cases,  
so that the scanner is bulletproof from day one.

### Acceptance Criteria
1: Write unit tests for story file discovery covering: empty directory, single file, 1000+ files, non-.md files, malformed YAML, missing frontmatter, concurrent modifications
2: Write tests for stories.json generation including: schema validation, empty arrays, order preservation, Unicode handling
3: Implement scanner only after all tests are written and failing
4: Performance tests verify 1000 stories scan under 5 seconds
5: Memory usage tests confirm under 100MB for large scans
6: Integration tests cover real file system operations
7: Test coverage for scanner must be 100%

## Story 1.3: Priority Queue Test Suite and Implementation

As a developer,  
I want to implement the priority queue with comprehensive test coverage,  
so that story processing order is guaranteed correct.

### Acceptance Criteria
1: Unit tests cover: empty queue, single story, status priority order, dependency respect, circular detection, missing dependencies, invalid status
2: Integration tests cover: deep dependency chains, mixed priorities, all blocked scenarios, queue persistence
3: Property-based tests ensure queue determinism and correct ordering
4: Performance benchmarks confirm O(n log n) complexity
5: Queue builds 1000 stories in under 100ms
6: Test coverage for queue must be 100%
7: Tests document expected behavior for all edge cases

## Story 1.4: Test Harness for Agent Mocking

As a developer,  
I want to create a comprehensive test harness for mocking agent interactions,  
so that we can test the full system without calling real APIs.

### Acceptance Criteria
1: Mock Claude SDK client with configurable responses
2: Mock agent response generator for SM, Dev, QA personas
3: Test data builders for creating story objects
4: Assertion helpers for verifying agent calls
5: Scenario runners for end-to-end test flows
6: Performance profiler integrated into test runs
7: Test report generator with failure analysis

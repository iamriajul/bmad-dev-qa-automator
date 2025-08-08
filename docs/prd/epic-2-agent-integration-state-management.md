# Epic 2: Agent Integration & State Management

**Goal**: Implement the Claude SDK integration and agent communication layer with comprehensive state management. All agent interactions must be predictable, traceable, and thoroughly tested.

## Story 2.1: Claude SDK Integration with TDD

As a developer,  
I want to integrate the Claude TypeScript SDK with full test coverage,  
so that agent communications are reliable and mockable.

### Acceptance Criteria
1: Write tests for SDK initialization: valid key, invalid key, missing key, network errors
2: Write tests for message sending: successful response, timeout, rate limit, API errors
3: Write tests for response streaming: partial responses, complete responses, stream interruption
4: Implement thin wrapper around Claude SDK only after tests pass
5: Retry logic tests: exponential backoff, max retries, retry on specific errors only
6: Context management tests: conversation history, context window limits
7: All SDK calls must be mockable for other test suites

## Story 2.2: Agent Prompt Templates and Testing

As a developer,  
I want to create tested prompt templates for each agent type,  
so that agent instructions are consistent and produce parseable outputs.

### Acceptance Criteria
1: Create parameterized prompt templates for SM, Dev, QA agents
2: Test templates include all required instructions (status change, signature)
3: Templates tested with mock responses to ensure parseability
4: Version control for prompts with migration strategy
5: Prompt validation ensures required parameters are provided
6: Templates support dry-run mode for testing
7: Documentation of expected agent response formats

## Story 2.3: Story Status State Machine

As a developer,  
I want to implement a state machine for story status transitions,  
so that only valid status changes are allowed.

### Acceptance Criteria
1: Write tests for all valid transitions: Draft→Approved, Approved→Dev, Dev→Ready For Review, Ready For Review→Done
2: Write tests for invalid transitions: Draft→Done, Done→Draft, etc.
3: Write tests for conditional transitions: Ready For Review→Dev (with QA findings)
4: State machine enforces signature requirements for transitions
5: State persistence tests: save/load state correctly
6: Concurrent state change prevention
7: State history tracking for audit trail

## Story 2.4: Agent Response Parser

As a developer,  
I want to implement a robust parser for agent responses,  
so that we can extract status, signatures, and findings reliably.

### Acceptance Criteria
1: Parser tests for well-formed responses: status changes, signatures, findings
2: Parser tests for malformed responses: missing fields, wrong format, unexpected content
3: Parser extracts status with fallback to previous status
4: Parser extracts signatures with timestamp validation
5: Parser extracts QA findings for SM revision
6: Parser handles multi-line findings and code blocks
7: Parser errors are descriptive for debugging

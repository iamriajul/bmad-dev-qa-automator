# Epic 3: Workflow Automation & Recovery

**Goal**: Build the complete automation workflows that orchestrate agents through story lifecycles, with robust error handling and recovery mechanisms.

## Story 3.1: SM-QA Approval Workflow

As a developer,  
I want to implement the SM-QA story approval workflow,  
so that Draft stories can be automatically reviewed and approved.

### Acceptance Criteria
1: Test workflow for simple approval: Draft → QA Review → Approved
2: Test workflow for rejection: Draft → QA Review → Draft (with findings) → SM Revision
3: Test infinite loop prevention: Max 3 revision cycles before escalation
4: Workflow preserves all signatures and findings in story file
5: stories.json updates after each state change
6: Workflow handles agent timeout gracefully
7: End-to-end test with mock agents completes in under 30 seconds

## Story 3.2: Dev-QA Implementation Workflow

As a developer,  
I want to implement the Dev-QA implementation workflow,  
so that approved stories are automatically developed and reviewed.

### Acceptance Criteria
1: Test workflow: Approved → Dev Implementation → Ready For Review → QA Review → Done
2: Test workflow with fixes: Ready For Review → QA finds issues → Dev → Ready For Review
3: Test abandoned implementation detection: Dev status for >2 hours without progress
4: Circuit breaker after 5 Dev-QA cycles
5: Implementation files tracked in stories.json
6: Workflow handles large code generation responses
7: End-to-end test with mock agents verifies complete flow

## Story 3.3: Orchestration Engine

As a developer,  
I want to implement the main orchestration engine,  
so that all workflows run automatically based on story states.

### Acceptance Criteria
1: Engine starts with stories.json scan or generation
2: Engine processes stories in priority order
3: Engine invokes appropriate workflow based on story status
4: Engine handles workflow failures with retry logic
5: Engine logs all actions with timestamps
6: Engine supports graceful shutdown (Ctrl+C)
7: Engine provides dry-run mode for testing
8: Integration test runs 10 story automation cycle

## Story 3.4: Epic Completion Detection

As a developer,  
I want the system to detect when all epics are complete,  
so that it can exit gracefully without manual intervention.

### Acceptance Criteria
1: When no incomplete stories exist, system asks SM about remaining work
2: SM response parsed for epic completion signal
3: System updates stories.json with completion status
4: System generates final summary report
5: System exits with success code when all epics complete
6: Test covers multiple epic scenarios
7: Completion detection works after failures/retries

## Story 3.5: Monitoring and Observability

As a developer,  
I want comprehensive logging and monitoring,  
so that I can review overnight automation results.

### Acceptance Criteria
1: Structured JSON logs with log levels (debug, info, warn, error)
2: Separate log files per run with timestamp
3: Summary report shows: stories completed, time taken, errors encountered
4: Agent conversation logs preserved for debugging
5: Performance metrics: API calls, response times, token usage
6: Morning dashboard generator creates HTML summary
7: Logs are automatically rotated after 7 days
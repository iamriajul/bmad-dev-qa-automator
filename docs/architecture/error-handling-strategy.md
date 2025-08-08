# Error Handling Strategy

## General Approach

- **Error Model:** Hierarchical error classes with specific error codes
- **Exception Hierarchy:** BaseError → DomainError → SpecificErrors
- **Error Propagation:** Bubble up with context, handle at workflow level

## Logging Standards

- **Library:** Winston 3.17.0
- **Format:** JSON structured logs with timestamp, level, and context
- **Levels:** debug, info, warn, error
- **Required Context:**
  - Correlation ID: UUID per automation run
  - Service Context: Component name and method
  - User Context: Story being processed (no PII)

## Error Handling Patterns

### External API Errors

- **Retry Policy:** Exponential backoff with jitter (1s, 2s, 4s)
- **Circuit Breaker:** Open after 3 consecutive failures, test after 30s
- **Timeout Configuration:** 120s for agent responses
- **Error Translation:** Map Claude SDK errors to user-friendly messages

### Business Logic Errors

- **Custom Exceptions:** 
  - `StoryValidationError` - Invalid story format
  - `DependencyError` - Circular or missing dependencies
  - `StateTransitionError` - Invalid status change
- **User-Facing Errors:** Clear messages with resolution steps
- **Error Codes:** BMD-001 through BMD-999 for categorization

### Data Consistency

- **Transaction Strategy:** Atomic file writes for stories.json
- **Compensation Logic:** Revert story status on partial failure
- **Idempotency:** Re-running same command produces same result

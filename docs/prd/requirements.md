# Requirements

## Functional

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

## Non Functional

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

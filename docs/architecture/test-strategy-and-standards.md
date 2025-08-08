# Test Strategy and Standards

## Testing Philosophy

- **Approach:** Strict Test-Driven Development (TDD) - tests must exist before implementation
- **Coverage Goals:** 90% minimum across all files (enforced in CI)
- **Test Pyramid:** 70% unit tests, 20% integration tests, 10% end-to-end tests

## Test Types and Organization

### Unit Tests

- **Framework:** Jest 30.0.5
- **File Convention:** `{module}.test.ts` in parallel test directory
- **Location:** `tests/unit/` mirroring `src/` structure
- **Mocking Library:** jest-mock-extended 3.0.7
- **Coverage Requirement:** 95% for core modules, 90% for utilities

**AI Agent Requirements:**
- Generate tests for all public methods
- Cover edge cases and error conditions
- Follow AAA pattern (Arrange, Act, Assert)
- Mock all external dependencies

### Integration Tests

- **Scope:** Component interactions, file system operations, state persistence
- **Location:** `tests/integration/`
- **Test Infrastructure:**
  - **File System:** Use temp directories with real file operations
  - **Claude SDK:** Mock at SDK level, not HTTP level
  - **State Management:** Test full read-write cycles

### End-to-End Tests

- **Framework:** Jest with full system execution
- **Scope:** Complete automation cycles with mock agents
- **Environment:** Isolated test environment with fixture files
- **Test Data:** Predefined stories in `tests/fixtures/`

## Test Data Management

- **Strategy:** Builder pattern for test data creation
- **Fixtures:** `tests/fixtures/` with valid, invalid, and edge case examples
- **Factories:** Test data builders in `tests/test-utils/test-builders.ts`
- **Cleanup:** Automatic cleanup of temp files after each test

## Continuous Testing

- **CI Integration:** 
  - Pre-commit: Run related tests only
  - PR: Full test suite with coverage report
  - Main branch: Full suite + integration tests
- **Performance Tests:** Benchmark critical paths (dependency resolution, file parsing)
- **Security Tests:** Validate no API keys in logs, sanitized error messages

## Coverage Requirements by Module

| Module | Required Coverage |
|--------|------------------|
| Core (workflow, state) | 95% |
| Agents | 90% |
| Utils | 90% |
| Models | 85% |
| Config | 80% |

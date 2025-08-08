# Security

## Input Validation

- **Validation Library:** Zod 4.0.14 for runtime type checking
- **Validation Location:** At all entry points - CLI commands, file parsing, agent responses
- **Required Rules:**
  - All external inputs MUST be validated
  - Validation at API boundary before processing
  - Whitelist approach preferred over blacklist

## Authentication & Authorization

- **Auth Method:** API key authentication via environment variable
- **Session Management:** Stateless - each run is independent
- **Required Patterns:**
  - API key must never appear in logs or error messages
  - Validate API key format before use
  - Fail fast if API key is invalid

## Secrets Management

- **Development:** `.env` file (gitignored) with `ANTHROPIC_API_KEY`
- **Production:** Environment variables from shell or CI/CD secrets
- **Code Requirements:**
  - NEVER hardcode secrets
  - Access via configuration service only
  - No secrets in logs or error messages

## API Security

- **Rate Limiting:** Respect Claude API rate limits with exponential backoff
- **CORS Policy:** N/A - CLI tool
- **Security Headers:** N/A - CLI tool
- **HTTPS Enforcement:** All Claude API calls use HTTPS by default

## Data Protection

- **Encryption at Rest:** N/A - Local files rely on OS file permissions
- **Encryption in Transit:** HTTPS for all API calls
- **PII Handling:** No PII collected or stored
- **Logging Restrictions:** 
  - Never log API keys
  - Sanitize file paths in errors
  - Redact sensitive parts of agent responses

## Dependency Security

- **Scanning Tool:** npm audit / pnpm audit
- **Update Policy:** Monthly security updates, immediate for critical CVEs
- **Approval Process:** All new dependencies require review in PR

## Security Testing

- **SAST Tool:** ESLint security plugin
- **DAST Tool:** N/A - CLI tool
- **Penetration Testing:** N/A - Open source project

# Infrastructure and Deployment

## Infrastructure as Code

- **Tool:** N/A - Client-side CLI tool
- **Location:** N/A
- **Approach:** No cloud infrastructure required

## Deployment Strategy

- **Strategy:** NPM Package Publishing
- **CI/CD Platform:** GitHub Actions
- **Pipeline Configuration:** `.github/workflows/ci.yml` and `.github/workflows/release.yml`

## Environments

- **Development:** Local development environment with hot reload via ts-node
- **Testing:** Automated testing in CI pipeline before publishing
- **Production:** NPM Registry for global distribution

## Environment Promotion Flow

```text
Local Development → GitHub PR → CI Tests → Merge to main → Release Tag → NPM Publish
     ↓                  ↓            ↓            ↓              ↓            ↓
  npm run dev      Run tests    All pass    Approved      v1.0.0      npm install -g
```

## Rollback Strategy

- **Primary Method:** NPM version rollback (`npm install bmad-automator@previous-version`)
- **Trigger Conditions:** Critical bugs reported, test failures post-release
- **Recovery Time Objective:** < 5 minutes (immediate via NPM)

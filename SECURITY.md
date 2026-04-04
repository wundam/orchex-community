# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in Orchex, please report it responsibly.

**Do NOT open a public GitHub issue for security vulnerabilities.**

### How to Report

Email **security@orchex.dev** with:

1. A description of the vulnerability
2. Steps to reproduce (if applicable)
3. The version of Orchex you're using (`orchex --version`)
4. Your environment (OS, Node.js version, IDE)

### What to Expect

- **Acknowledgment** within 48 hours
- **Status update** within 7 business days
- **Fix timeline** communicated once the issue is assessed

### Scope

This policy covers:
- The `@wundam/orchex` npm package
- The Orchex MCP server (local mode)
- The `orchex` CLI tool

For issues with the Orchex cloud service (orchex.dev), please email the same address.

## Supported Versions

| Version | Supported |
|---------|-----------|
| 1.0.0-rc.x (latest) | Yes |
| < 1.0.0-rc.16 | No |

We only provide security fixes for the latest release candidate during the pre-1.0 period. After 1.0, we will follow standard semver support policies.

## Security Practices

- All npm releases are published with [provenance](https://docs.npmjs.com/generating-provenance-statements) (SLSA Level 3)
- 4 automated CI guards verify package contents on every release
- Server-side code is never included in the npm package
- API keys are stored locally with restricted file permissions (0600)
- No telemetry or data collection in the npm package

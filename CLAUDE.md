# CLAUDE.md - AI Assistant Guide for Finxact-MCP

> **Last Updated**: 2026-01-22
> **Repository**: tmah2012/Finxact-MCP
> **Purpose**: Model Context Protocol (MCP) integration for Finxact

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Repository Structure](#repository-structure)
3. [Development Workflow](#development-workflow)
4. [Key Conventions](#key-conventions)
5. [MCP-Specific Guidelines](#mcp-specific-guidelines)
6. [Finxact Integration](#finxact-integration)
7. [Git Operations](#git-operations)
8. [Testing & Quality](#testing--quality)
9. [Security Considerations](#security-considerations)
10. [Common Tasks](#common-tasks)

---

## 🎯 Project Overview

### Purpose
Finxact-MCP provides a Model Context Protocol server integration for Finxact, enabling AI assistants to interact with Finxact's core banking platform through a standardized interface.

### Key Goals
- Expose Finxact APIs through MCP tools and resources
- Provide secure, authenticated access to banking operations
- Enable AI-assisted banking workflows and analysis
- Maintain compliance with financial services regulations

### Technology Stack
- **MCP Framework**: Model Context Protocol SDK
- **Finxact Platform**: Core banking platform
- **Language**: (To be determined - typically TypeScript/Python for MCP servers)
- **Authentication**: (To be determined based on Finxact requirements)

---

## 📁 Repository Structure

```
Finxact-MCP/
├── CLAUDE.md              # This file - AI assistant guide
├── README.md              # Project documentation
├── src/                   # Source code
│   ├── server/           # MCP server implementation
│   ├── tools/            # MCP tool definitions
│   ├── resources/        # MCP resource providers
│   └── finxact/          # Finxact API integration
├── tests/                 # Test suite
├── docs/                  # Additional documentation
├── config/                # Configuration files
└── examples/              # Usage examples
```

**Note**: This structure will be updated as the project evolves.

---

## 🔄 Development Workflow

### Branch Strategy

#### Feature Branches
- All development must occur on feature branches
- Branch naming: `claude/claude-md-<session-id>`
- Current branch: `claude/claude-md-mkq3qfl5bsbyxqrb-D9gwv`

#### Branch Operations
```bash
# Check current branch
git branch

# Create and switch to feature branch (if needed)
git checkout -b claude/claude-md-<session-id>

# Push changes
git push -u origin claude/claude-md-<session-id>
```

### Commit Guidelines

#### Commit Message Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `refactor`: Code refactoring
- `test`: Test additions/changes
- `chore`: Build/tooling changes

**Examples**:
```bash
feat(tools): add account query MCP tool
fix(auth): resolve token refresh issue
docs(readme): update installation instructions
```

#### Commit Process
1. Stage relevant changes: `git add <files>`
2. Review diff: `git diff --staged`
3. Commit with descriptive message
4. Push to feature branch

### Pull Request Process
1. Ensure all tests pass
2. Update documentation if needed
3. Create PR with comprehensive description:
   - Summary of changes
   - Motivation and context
   - Testing performed
   - Breaking changes (if any)

---

## 🎨 Key Conventions

### Code Style

#### General Principles
- **Simplicity First**: Avoid over-engineering
- **YAGNI**: Don't add features not explicitly requested
- **DRY with Caution**: Don't abstract until patterns emerge (3+ uses)
- **Clear over Clever**: Prioritize readability

#### Specific Guidelines
- Use meaningful variable/function names
- Keep functions focused and small
- Comment only when logic isn't self-evident
- Avoid premature optimization
- No unused code (delete, don't comment out)

### File Organization
- One primary export per file
- Group related functionality
- Separate concerns (API, business logic, presentation)
- Keep test files alongside source files

### Error Handling
- Validate at system boundaries only
- Use appropriate error types
- Include context in error messages
- Don't catch errors you can't handle

### Documentation
- Update CLAUDE.md when architecture changes
- Keep README.md current
- Document public APIs
- Include usage examples
- No inline docs for obvious code

---

## 🔌 MCP-Specific Guidelines

### MCP Server Structure

#### Server Implementation
```typescript
// Example structure - adjust based on actual implementation
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({
  name: "finxact-mcp",
  version: "1.0.0",
}, {
  capabilities: {
    tools: {},
    resources: {},
  },
});
```

#### Tool Definitions
- Each tool should have a clear, single purpose
- Include comprehensive input schemas
- Provide descriptive tool names and descriptions
- Return structured, consistent outputs
- Handle errors gracefully

#### Resource Providers
- Use URIs following pattern: `finxact://<resource-type>/<id>`
- Implement efficient caching where appropriate
- Support resource listing and retrieval
- Include metadata in resource responses

### MCP Best Practices

#### Tool Design
1. **Atomic Operations**: Each tool performs one clear action
2. **Idempotent**: Same inputs produce same results
3. **Error Reporting**: Clear error messages with actionable info
4. **Validation**: Validate inputs before processing
5. **Documentation**: Inline schema descriptions

#### Security
- Never expose sensitive data in tool responses
- Validate and sanitize all inputs
- Implement proper authentication
- Log security-relevant events
- Follow principle of least privilege

---

## 🏦 Finxact Integration

### API Integration Points

#### Core Banking Operations
- Account management
- Transaction processing
- Customer data access
- Product configuration
- Reporting and analytics

#### Authentication
- Implement secure credential management
- Support token refresh mechanisms
- Handle session expiration gracefully
- Never log credentials or tokens

### Finxact-Specific Considerations

#### Data Handling
- Respect data privacy regulations (PII, PCI-DSS)
- Implement field-level encryption where required
- Audit all data access
- Support data masking for non-production environments

#### API Rate Limiting
- Implement exponential backoff
- Cache responses where appropriate
- Batch operations when possible
- Monitor API quota usage

#### Compliance
- Maintain audit trails
- Support compliance reporting
- Implement transaction reversals properly
- Follow financial regulations (SOX, etc.)

---

## 🔐 Git Operations

### Push Operations
```bash
# Always use -u flag for first push
git push -u origin <branch-name>

# Branch must start with 'claude/' and match session ID
# Format: claude/claude-md-<session-id>
```

### Retry Logic for Network Issues
- Retry failed push/pull up to 4 times
- Use exponential backoff: 2s, 4s, 8s, 16s
- Only retry on network errors, not auth failures

### Safety Protocols
- **NEVER** update git config without permission
- **NEVER** force push to main/master
- **NEVER** skip hooks (--no-verify)
- **NEVER** run destructive commands (hard reset) without confirmation
- **ALWAYS** verify branch before pushing

---

## ✅ Testing & Quality

### Testing Strategy

#### Unit Tests
- Test individual functions and modules
- Mock external dependencies (Finxact API)
- Aim for high coverage of business logic
- Keep tests fast and isolated

#### Integration Tests
- Test MCP tool interactions
- Test Finxact API integration
- Use test/sandbox environments
- Never test against production

#### Test Organization
```
tests/
├── unit/           # Unit tests
├── integration/    # Integration tests
├── fixtures/       # Test data
└── mocks/          # Mock implementations
```

### Quality Checks
- Run linters before committing
- Ensure all tests pass
- Check for security vulnerabilities
- Review error handling
- Validate input schemas

---

## 🔒 Security Considerations

### Critical Security Rules

#### Authentication & Authorization
- Store credentials securely (environment variables, vaults)
- Implement proper session management
- Validate all authentication tokens
- Support role-based access control (RBAC)

#### Input Validation
- Validate all inputs at system boundaries
- Sanitize data before passing to Finxact APIs
- Prevent injection attacks (SQL, command, etc.)
- Implement request size limits

#### Data Protection
- Encrypt sensitive data at rest and in transit
- Implement data masking for logs
- Never expose internal error details to clients
- Follow PCI-DSS for payment card data

#### Audit & Monitoring
- Log all API calls with timestamps
- Track authentication events
- Monitor for suspicious patterns
- Implement alerting for security events

### OWASP Top 10 Prevention
- Broken Access Control: Implement proper authorization
- Cryptographic Failures: Use strong encryption
- Injection: Sanitize all inputs
- Insecure Design: Security by design
- Security Misconfiguration: Secure defaults
- Vulnerable Components: Keep dependencies updated
- Authentication Failures: Multi-factor where possible
- Data Integrity Failures: Verify data integrity
- Logging Failures: Comprehensive audit logs
- SSRF: Validate external requests

---

## 🛠️ Common Tasks

### Adding a New MCP Tool

1. **Define the tool schema**:
   - Create tool definition with input schema
   - Document parameters clearly
   - Define expected output format

2. **Implement tool handler**:
   - Validate inputs
   - Call Finxact API
   - Handle errors appropriately
   - Return structured response

3. **Add tests**:
   - Unit tests for handler logic
   - Integration tests with mocked Finxact API
   - Error case coverage

4. **Update documentation**:
   - Add to README.md
   - Include usage examples
   - Document any limitations

### Debugging MCP Integration

#### Enable Debug Logging
```bash
# Set environment variable
export MCP_DEBUG=true
```

#### Test Tool Directly
```bash
# Use MCP inspector or test client
# Example structure - adjust based on implementation
mcp-inspector <server-command>
```

#### Common Issues
- **Tool not found**: Check tool registration
- **Schema validation failed**: Review input schema
- **Finxact API error**: Check credentials and endpoint
- **Timeout**: Implement retry logic

### Updating Dependencies

```bash
# Check for updates
npm outdated  # or pip list --outdated

# Update with caution
npm update    # or pip install --upgrade

# Test thoroughly after updates
npm test
```

---

## 📚 Additional Resources

### MCP Documentation
- [MCP Specification](https://modelcontextprotocol.io)
- [MCP SDK Documentation](https://github.com/modelcontextprotocol/sdk)
- [MCP Best Practices](https://modelcontextprotocol.io/docs/best-practices)

### Finxact Documentation
- Finxact API Documentation (internal)
- Finxact Developer Portal
- Finxact Authentication Guide

### Development Tools
- MCP Inspector for testing
- API testing tools (Postman, Insomnia)
- Git workflow tools

---

## 🔄 Maintenance

### Keeping CLAUDE.md Updated

This file should be updated when:
- Project structure changes
- New conventions are established
- Integration patterns emerge
- Dependencies change significantly
- Security requirements evolve

### Review Schedule
- Review quarterly or after major changes
- Update version/date at top of file
- Archive old patterns that are deprecated
- Add new common tasks as they emerge

---

## 📝 Notes for AI Assistants

### Working on This Project

1. **Always read before modifying**: Never propose changes to code you haven't read
2. **Use TodoWrite**: Track multi-step tasks with the TodoWrite tool
3. **Prefer existing files**: Edit rather than create new files
4. **Test thoroughly**: Run tests before committing
5. **Security first**: Consider security implications of all changes
6. **Document as you go**: Update docs when changing behavior

### Task Execution Pattern

```
1. Read relevant code
2. Create todo list for complex tasks
3. Implement changes incrementally
4. Mark todos as completed
5. Test changes
6. Commit with clear message
7. Push to feature branch
```

### Anti-Patterns to Avoid

- ❌ Creating files without reading existing code
- ❌ Over-engineering simple solutions
- ❌ Adding features not requested
- ❌ Skipping tests
- ❌ Committing without user request
- ❌ Force pushing
- ❌ Modifying git config

### Communication Style

- Be concise and technical
- No emojis unless requested
- Output facts, not validation
- Disagree when necessary
- No timeline estimates
- Clear error explanations

---

## 🏁 Getting Started Checklist

For new AI assistants working on this project:

- [ ] Read this entire CLAUDE.md file
- [ ] Review current branch and git status
- [ ] Understand the MCP specification basics
- [ ] Familiarize with Finxact API documentation
- [ ] Check for existing tests and patterns
- [ ] Review open issues/PRs if any
- [ ] Understand security requirements
- [ ] Set up development environment

---

**Remember**: When in doubt, ask questions, read code, and prioritize security and simplicity.

---

*This guide is a living document. Update it as the project evolves.*

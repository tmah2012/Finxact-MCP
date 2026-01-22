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

## 🔧 Finxact REST API Best Practices

### 1. Core Objectives & Guarantees

#### API-First Design
- Thousands of RESTful endpoints engineered for scalability and security
- All operations follow REST principles
- Comprehensive JSON schema validation

#### ACID Properties
Finxact APIs guarantee ACID compliance:

- **Atomicity**: If any step fails in multi-record creation, the entire transaction is rolled back
- **Consistency**: Data validated against strict JSON schema and enforced by database constraints
- **Isolation**: Each API call runs in an independent context
- **Durability**: Detailed transaction logs guarantee persistence

### 2. Idempotency

Understanding idempotency is critical for reliable API integration:

#### Inherently Idempotent Methods
- `GET` and `DELETE` are naturally idempotent

#### Version-Controlled Methods
- `PUT` and `PATCH` require the current version (`_vn`)
- Version mismatches result in **409 Conflict**
- Always fetch current version before updates

#### POST with Idempotency Keys
- `POST` requests are non-idempotent by default
- Use `x-idempotency-key` header for safe retries
- Same key returns same response for duplicate requests

**Example POST with Idempotency Key**:
```http
POST /model/v1/addr HTTP/1.1
Host: api.finxact.io
client_id: your_client_id
secret: your_secret
Content-Type: application/json
Fnx-Header: {"identity":{"userRoles":["developer"]}}
x-idempotency-key: 9b7302b3-13bf-42c4-8a19-509e271bfc11

{
  "city": "Plymouth Meeting",
  "cntry": "US",
  "postCode": "19462",
  "region": "PA",
  "street": "NewWay Avenue"
}
```

**Response (HTTP 201)**:
```json
{
  "_Id": "4iIN-rdBXRDJXV--V5F-Co-",
  "city": "Plymouth Meeting",
  "cntry": "US",
  "postCode": "19462",
  "region": "PA",
  "street": "NewWay Avenue",
  "_vn": 0,
  "_cDtm": "2022-03-17T18:24:15Z"
}
```

### 3. Traceability & Framework Fields

Every Finxact record includes metadata for full auditability:

#### Core Framework Fields

| Field | Description | Example Value |
|-------|-------------|---------------|
| `_Id` | Primary key (TGUID, case-sensitive) | `"4iIN-rdBXRDJXV--V5F-Co-"` |
| `_vn` | Version number (increments on updates) | `3` |
| `_cDtm` | Creation timestamp | `"2022-03-17T18:24:15Z"` |
| `_uDtm` | Last update timestamp | `"2022-03-17T18:26:20Z"` |
| `_cLogRef` | Reference to creation message log | `"4j666ERO8xbqUk----5F----"` |
| `_uLogRef` | Reference to last update message log | `"4jHlkXSbPG0g2----F1F----"` |
| `_cLog` | Creation log TGUID | `"4j666ERO8xbqUk----5F----"` |
| `_uLog` | Update log TGUID | `"4jHlkXSbPG0g2----F1F----"` |
| `_schVn` | Schema version number | `2` |
| `_Ix` | Array element index (auto-generated) | `"1"` |
| `_flags` | State flags (2-byte) | `0` (normal state) |

**Sample Record with Framework Fields**:
```json
{
  "_Id": "4iIN-rdBXRDJXV--V5F-Co-",
  "acctNbr": "000000000013",
  "_vn": 1,
  "_cLogRef": "4j666ERO8xbqUk----5F----",
  "_uLogRef": "4jHlkXSbPG0g2----F1F----",
  "_cDtm": "2022-03-17T18:25:53Z",
  "_uDtm": "2022-03-17T18:26:20Z"
}
```

### 4. Authentication & Request Headers

#### Required Headers

All Finxact API requests must include:

```json
{
  "client_id": "your_client_id",
  "secret": "your_secret",
  "Content-Type": "application/json",
  "Fnx-Header": "{\"identity\":{\"userRoles\":[\"developer\"]}}"
}
```

#### Optional Headers
- `x-idempotency-key`: For POST request idempotency (UUID format recommended)

#### Security Best Practices
- **NEVER** hardcode credentials in source code
- Store `client_id` and `secret` in environment variables or secure vaults
- Rotate credentials regularly
- Use role-based access through `userRoles` in `Fnx-Header`

### 5. HTTP Methods & Response Codes

#### Supported HTTP Methods
- `GET`: Retrieve resources
- `POST`: Create new resources
- `PUT`: Replace entire resource
- `PATCH`: Partial update
- `DELETE`: Remove resource
- `OPTIONS`: Get allowed methods

#### Response Codes Reference

| Code | Meaning | Common Cause | Action |
|------|---------|--------------|--------|
| 200 | OK | Successful GET/PUT/PATCH | Process response |
| 201 | Created | Successful POST | Resource created |
| 400 | Bad Request | Missing required fields, invalid JSON | Validate payload against schema |
| 401 | Unauthorized | Invalid credentials | Check `client_id` and `secret` |
| 403 | Forbidden | IP not whitelisted, insufficient permissions | Verify network access and roles |
| 404 | Not Found | Invalid endpoint or resource ID | Verify URL and resource existence |
| 405 | Method Not Allowed | Wrong HTTP method | Check API documentation |
| 408 | Request Timeout | Request took too long | Retry with exponential backoff |
| 409 | Conflict | Version mismatch, duplicate idempotency key | Re-fetch resource, verify `_vn` |
| 429 | Too Many Requests | Rate limit exceeded | Honor `Retry-After` header |
| 500 | Internal Server Error | Server-side issue | Check logs, retry later |
| 502 | Bad Gateway | Gateway issue | Retry with backoff |
| 503 | Service Unavailable | Temporary unavailability | Retry with backoff |

**Example Successful GET Response (HTTP 200)**:
```json
{
  "_Id": "4iIN-rdBXRDJXV--V5F-Co-",
  "acctNbr": "000000000013",
  "acctTitle": "John Smith"
}
```

### 6. JSON Schema & Special Data Types

#### Schema Structure
Finxact uses JSON Schema to define all models with:
- Header metadata
- Properties list with types and formats
- Validation rules and constraints

#### Special Data Types

##### TGUID (Temporal Globally Unique Identifier)
- Case-sensitive unique identifier with embedded timestamp
- Format: `"4iIN-rdBXRDJXV--V5F-Co-"`
- Used for `_Id` and reference fields

```json
"acctId": {
  "title": "Account Identifier",
  "description": "A unique TGUID",
  "type": "string",
  "format": "tguid"
}
```

##### Encrypted Fields
- Properties with `"format": "encrypt"`
- Secured in transit and at rest
- Never expose in logs or debug output

##### Frequency
- Recurring intervals: `"monthly"`, `"quarterly"`, `"weekly"`, etc.

```json
"billingCycle": {
  "title": "Billing Cycle",
  "description": "Recurring interval for billing",
  "type": "string",
  "format": "frequency",
  "default": "monthly"
}
```

##### Duration
- ISO 8601 duration format
- Example: `"P1M"` (one month), `"P1Y"` (one year)

##### Date and DateTime
- ISO 8601 format
- Date: `"2022-03-17"`
- DateTime: `"2022-03-17T18:24:15Z"`

##### URI
- Valid URI format for links and references

### 7. Collection Types and Nuances

#### Simple Arrays
Basic lists of primitive values:

```json
"pastDueTerms": {
  "title": "Past Due Terms",
  "description": "List of counter terms",
  "type": "array",
  "items": { "type": "string" }
}
```

#### Implicit Keyed Arrays
Arrays of objects without explicit primary key; system generates `_Ix`:

```json
{
  "_Ix": 1,
  "email": "john.doe@example.com"
}
```

#### Explicit Keyed Arrays
Objects with defined keys via `x-dbInterface.primaryKey`:

```json
{
  "memberId": "1000010",
  "partyTitle": "Customer",
  "startDtm": "2022-03-17T18:24:15Z"
}
```

#### Maps
Dynamic key-value storage using `additionalProperties`:

```json
"localeData": {
  "title": "Locale Data",
  "description": "Key-value pairs for locale-specific information",
  "type": "object",
  "additionalProperties": { "type": "string" }
}
```

### 8. x-dbInterface & x-config Advanced Attributes

#### x-dbInterface Metadata

Adds persistence and relationship metadata to schemas:

```json
"x-dbInterface": {
  "primaryKey": ["_Id"],
  "foreignKeys": [{
    "name": "party",
    "foreignKey": ["partyId"],
    "referenceEntity": "party.json",
    "referenceKey": ["_Id"]
  }],
  "indexes": [{
    "name": "acctByCustId",
    "indexKey": ["groupId", "custId"],
    "isUnique": true
  }],
  "temporal": { "option": 4 }
}
```

#### Key x-dbInterface Properties

- **primaryKey**: Defines unique identifier(s)
- **foreignKeys**: Establishes relationships between entities
- **indexes**: Performance optimization for queries
- **temporal**: Enables history tracking (options 1-4)
- **serialize**: Custom serialization logic
- **computeds**: Computed/derived fields
- **observedProperties**: Properties that trigger events
- **hasExtents**: Support for extended data
- **extends**: Schema inheritance

#### x-config for Configuration Models

```json
"x-config": {
  "mergeType": "global",  // or "local" or "mixed"
  "readOnly": ["_Id", "_vn"],
  "ignoreColumns": ["tempData"],
  "conditional": {...}
}
```

#### x-cached for Caching Strategy

```json
"x-cached": {
  "expiry": "24h"
}
```

### 9. API Request Sequence

Understanding the full request lifecycle:

```
Client Request
    ↓
Message Request Log (msgRq) Created
    ↓
Request Processing & Validation
    ↓
Data Persistence with Framework Fields
    (_cLogRef, _uLogRef, _vn, timestamps)
    ↓
Message Response Log (msgRs) Created
    ↓
Response Returned to Client
```

**Key Points**:
- Every request is logged (`msgRq`)
- Every response is logged (`msgRs`)
- All changes are traceable via log references
- Framework fields automatically populated

### 10. Temporal vs. Non-Temporal Models

#### Temporal Models
- Capture every change for historical queries
- Support `_asOfDt` and `_vn` queries
- Enable audit trail and compliance reporting

**Example: Query Historical State**:
```http
GET /model/v1/posn_dep/4iIN-rdBXRDJXV--V5F-Co-?_asOfDt=2022-04-29T12:00:08Z
```

#### Non-Temporal Models
- Store only current state
- Lighter weight, faster queries
- Use for configuration and reference data

#### Determining Model Type
Check schema's `x-dbInterface.temporal.option`:
- `option: 0`: Non-temporal
- `option: 1-4`: Various temporal modes

### 11. Swagger Documentation

#### Accessing Swagger
- Available in Finxact Console
- Navigate to: **Resources → Swagger**
- Interactive endpoint exploration
- Schema browsing and validation

#### Swagger Features
- Endpoint documentation with examples
- Request/response schemas
- Filter syntax reference
- Try-it-out functionality
- Model definitions

### 12. Querying, Filtering & Advanced Ordering

#### Basic Filtering

**Simple equality filter**:
```http
GET /model/v1/party_person?filter.q=lastName='Smith'
```

**Wildcard matching** (URL-encode `%` as `%25`):
```http
GET /model/v1/party_person?filter.q=UPPER(firstName) like UPPER('will%25')
```

#### Nested Property Filtering

**Slash notation for nested objects**:
```http
GET /model/v1/posn_dep?filter.q=acctgSeg/deptId != '350'
```

**Dot notation for child arrays**:
```http
GET /model/v1/party_person?filter.q=emails.data='john.Levy@test.com'
```

#### Parent/Child Reference Filtering

**Using ../ to reference parent**:
```http
GET /model/v1/trn/~/entries?filter.q=acctGroup != 2 and ../network='ACH'
```

#### Field Inclusion/Exclusion

**Include specific fields only**:
```http
GET /model/v1/acct_bk?filter.include=acctTitle
```

**Exclude fields** (use `@children` to exclude all child objects):
```http
GET /model/v1/trn?filter.exclude=@children,mode,batchId
```

#### Ordering Results

**Sort ascending**:
```http
GET /model/v1/prod_bk?filter.orderBy=ifxAcctType
```

**Sort descending** (prefix with `-`):
```http
GET /model/v1/prod_bk?filter.orderBy=-ifxAcctType
```

**Multiple sort fields** (comma-separated):
```http
GET /model/v1/prod_bk?filter.orderBy=ifxAcctType,-avlStartDtm
```

### 13. Pagination Methods

#### A. Keyset Pagination (Recommended)

Most efficient for large datasets; uses cursor-based navigation.

**Initial Request**:
```http
GET /model/v1/posn_dep?filter.q=acctGroup=1
```

**Response**:
```json
{
  "limit": 1000,
  "next": "eyJLZXlzIjpbIl9JZCJdLCJWYWxzIjpbIjRqN1BoSV8wU3RQX2lGLS0tVmVGLUNvLSJdLCJPcCI6Ilx1MDAzZT0ifQ==",
  "data": [...]
}
```

**Next Page Request**:
```http
GET /model/v1/posn_dep?filter.q=acctGroup=1&filter.next=eyJLZXlzIjpbIl9JZCJdLCJWYWxzIjpbIjRqN1BoSV8wU3RQX2lGLS0tVmVGLUNvLSJdLCJPcCI6Ilx1MDAzZT0ifQ==
```

**Benefits**:
- Consistent performance regardless of page depth
- No skipped or duplicate records
- Handles concurrent modifications gracefully

#### B. Offset Pagination

Easier to implement but slower for deep pagination.

**Request with Page Number**:
```http
GET /model/v1/posn_dep?filter.q=acctGroup=1&filter.page=1
```

**Response**:
```json
{
  "limit": 1000,
  "page": 1,
  "totalCount": 20188,
  "numPages": 21,
  "data": [...]
}
```

**Combine with Ordering**:
```http
GET /model/v1/posn_dep?filter.q=acctGroup=1&filter.page=1&filter.orderBy=-_cDtm
```

### 14. Special Purpose Filters

#### Historical Queries with _asOfDt

Retrieve record state at specific point in time (temporal models only):

```http
GET /model/v1/posn_dep/4iIN-rdBXRDJXV--V5F-Co-?_asOfDt=2022-04-29T12:00:08Z
```

#### Filter by Last Update

```http
GET /model/v1/posn_dep?filter.q=_lastUpdateDtm >= '2022-04-01T00:00:00Z'
```

#### Version-Specific Queries

**Single version**:
```http
GET /model/v1/posn_dep/4iIN-rdBXRDJXV--V5F-Co-?_vn=3
```

**Version range** (inclusive):
```http
GET /model/v1/posn_dep/4iIN-rdBXRDJXV--V5F-Co-?_vn=3~5
```

#### Include Computed Properties

```http
GET /model/v1/posn_dep/4iIN-rdBXRDJXV--V5F-Co-?_comp=true
```

**Warning**: Computed properties can be expensive. Limit to single record queries or small result sets.

### 15. Extended Troubleshooting & Remediation

#### HTTP 400 Bad Request

**Causes**:
- Missing required fields
- Invalid JSON syntax
- Schema validation failure
- Invalid data types

**Remediation**:
1. Validate payload against schema (use Swagger)
2. Check required fields are present
3. Verify data types match schema
4. Ensure proper JSON formatting

#### HTTP 401 Unauthorized / 403 Forbidden

**Causes**:
- Invalid `client_id` or `secret`
- IP address not whitelisted (403)
- Insufficient role permissions (403)

**Remediation**:
1. Verify credentials are correct
2. Check IP whitelist configuration
3. Confirm `userRoles` in `Fnx-Header`
4. Contact administrator for access

#### HTTP 409 Conflict

**Causes**:
- Version mismatch (stale `_vn`)
- Duplicate idempotency key
- Concurrent modification

**Remediation**:
1. Re-fetch resource to get current `_vn`
2. Retry update with new version
3. For idempotency key conflicts, verify if previous request succeeded
4. Implement optimistic locking retry logic

**Example Retry Logic**:
```javascript
async function updateWithRetry(resourceId, updates, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    // Fetch current version
    const current = await getResource(resourceId);

    // Attempt update with current version
    try {
      return await updateResource(resourceId, {
        ...updates,
        _vn: current._vn
      });
    } catch (error) {
      if (error.status === 409 && i < maxRetries - 1) {
        // Version conflict, retry
        continue;
      }
      throw error;
    }
  }
}
```

#### HTTP 429 Too Many Requests

**Causes**:
- Exceeded rate limits
- Too many concurrent requests

**Remediation**:
1. Check `Retry-After` header for wait time
2. Implement exponential backoff
3. Reduce request frequency
4. Batch operations where possible
5. Implement request queuing

**Example Rate Limit Handler**:
```javascript
async function requestWithRateLimit(url, options) {
  try {
    return await fetch(url, options);
  } catch (error) {
    if (error.status === 429) {
      const retryAfter = error.headers.get('Retry-After') || 60;
      await sleep(retryAfter * 1000);
      return requestWithRateLimit(url, options);
    }
    throw error;
  }
}
```

#### HTTP 500/502/503 Server Errors

**Causes**:
- Server-side issues
- Temporary service disruption
- Gateway problems

**Remediation**:
1. Implement retry with exponential backoff
2. Check server logs if available
3. Contact support if persistent
4. Implement circuit breaker pattern

**Example Exponential Backoff**:
```javascript
async function retryWithBackoff(fn, maxRetries = 4) {
  const delays = [2000, 4000, 8000, 16000]; // 2s, 4s, 8s, 16s

  for (let i = 0; i <= maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries || ![500, 502, 503].includes(error.status)) {
        throw error;
      }
      await sleep(delays[i]);
    }
  }
}
```

### Integration Checklist for MCP Tools

When creating MCP tools that interact with Finxact:

- [ ] Include `client_id` and `secret` from secure configuration
- [ ] Set proper `Fnx-Header` with user roles
- [ ] Use `x-idempotency-key` for POST operations
- [ ] Handle all documented HTTP response codes
- [ ] Implement version checking for PUT/PATCH
- [ ] Support pagination for list operations
- [ ] Include error context in responses
- [ ] Never log sensitive fields (encrypted, credentials)
- [ ] Implement retry logic with exponential backoff
- [ ] Validate inputs against JSON schema
- [ ] Support filtering and ordering parameters
- [ ] Document temporal vs non-temporal model behavior
- [ ] Cache responses appropriately (honor `x-cached`)
- [ ] Track API quota usage
- [ ] Log all operations for audit trail

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

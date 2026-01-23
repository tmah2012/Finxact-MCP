MCP is between Claude and Finxact Core 

Sample API's https://finxact.io/sampleAPIs 
Schema overview https://finxact.io/schema/overview
Docs https://finxact.io/docs/

Finxact REST API best practices:

1. Core Objectives & Guarantees
  - API-first design provides thousands of RESTful endpoints for core banking-engineered for scalability and security.
  - ACID Properties:
    - Atomicity: If any step fails (e.g., in multi-record creation), the entire transaction is rolled back.
    - Consistency: Data is validated against a strict JSON schema and enforced by DB constraints.
    - Isolation: Each API call runs in an independent context.
    - Durability: Detailed transaction logs guaran
2. Idempotency
  - GET and DELETE are inherently idempotent.
  - PUT and PATCH require inclusion of the current version (_vn); mismatches result in a 409 Conflict.
  - POST requests are non-idempotent unless an x-idempotency-key is provided.
    Sample POST Request With Idempotency Key:

    Request:
      POST /model/v1/addr HTTP/1.1
      Host: api.finxact.io
      client_id: your_client_id
      secret: your_secret
      Content-Type: application/json
      Fnx-Header: {"identity":{"userRoles":["developer"]}}
      x-idempotency-key: 9b7302b3-13bf-42c4-8a19-509e271bfc11

    Body:
      {
        "city": "Plymouth Meeting",
        "cntry": "US",
        "postCode": "19462",
        "region": "PA",
        "street": "NewWay Avenue"
      }

    Response (if processed successfully, HTTP 201):
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

3. Traceability & Framework Fields
  - Every record carries fields to ensure full auditability:
    - _Id: Primary key in TGUID format (case-sensitive; e.g., "4iIN-rdBXRDJXV--V5F-Co-").
    - _cLogRef: Reference to the message request log (msgRq).
    - _uLogRef: Reference to the last update's log (msgRs).
    - _vn: Version count that increments with updates (sample value: 3).
    - _schVn: Schema version number, for example, 2.
    - _cLog / _uLog: TGUIDs representing creation and update logs.
    - _cDtm / _uDtm: Timestamps for when the record was created or last updated.
    - _Ix: An auto-generated index for array elements (e.g., "1" for the first element).
    - _flags: A 2-byte flag indicating current state (e.g., 0 for normal).

    Sample Record:
      {
        "_Id": "4iIN-rdBXRDJXV--V5F-Co-",
        "acctNbr": "000000000013",
        "_vn": 1,
        "_cLogRef": "4j666ERO8xbqUk----5F----",
        "_uLogRef": "4jHlkXSbPG0g2----F1F----",
        "_cDtm": "2022-03-17T18:25:53Z",
        "_uDtm": "2022-03-17T18:26:20Z"
      }

4. Authentication & Request Headers
  - Required headers include:
    - client_id and secret (assigned per role/environment).
    - Content-Type: application/json
    - Fnx-Header: For example, {"identity":{"userRoles":["developer"]}}
    - Optionally, x-idempotency-key (for POST).

    Sample Header JSON:
      {
        "client_id": "your_client_id",
        "secret": "your_secret",
        "Content-Type": "application/json",
        "Fnx-Header": "{\\"identity\\":{\\"userRoles\\":[\\"developer\\"]}}"
      }

5. HTTP Methods & Detailed Response Codes
  - Supported methods: GET, POST, PUT, PATCH, DELETE, OPTIONS.
  - Response Codes (with examples):
    - 200/201: Successful retrieval/creation.
      Example GET response (HTTP 200):
        {
          "_Id": "4iIN-rdBXRDJXV--V5F-Co-",
          "acctNbr": "000000000013",
          "acctTitle": "John Smith"
        }
    - 400: Bad Request - e.g., missing required fields.
    - 401: Unauthorized - check credentials.
    - 403: Forbidden - possible IP whitelist issue.
    - 404: Not Found - incorrect endpoint.
    - 405: Method Not Allowed.
    - 408: Request Timeout.
    - 409: Conflict - version mismatch or duplicate idempotency key.
    - 429: Too Many Requests - observe Retry-After header.
    - 500, 502, 503: Server errors - troubleshoot using server logs.

6. JSON Schema & Special Data Types
  - Schemas define models with a header and properties list.
  - Special Data Types:
    - TGUID: A temporal unique identifier (e.g., "4iIN-rdBXRDJXV--V5F-Co-") that carries date/time info and is case-sensitive.
    - Encryption: Properties formatted as "encrypt" are secured in transit and at rest (no sample value is exposed for security).
    - Frequency: Strings such as "monthly" or "quarterly" denote recurring intervals.
    - Duration: Formats like ISO 8601 duration ("P1M" for one month) depict time spans.
    - Date, Date-Time, and URI: Standard values ensuring valid temporal and link formatting.

    Sample Property Definition for TGUID and Frequency:
      "acctId": {
        "title": "Account Identifier",
        "description": "A unique TGUID",
        "type": "string",
        "format": "tguid"
      },
      "billingCycle": {
        "title": "Billing Cycle",
        "description": "Recurring interval for billing",
        "type": "string",
        "format": "frequency",
        "default": "monthly"
      }

7. Collection Types and Nuances
  - Simple Arrays: E.g., a list of pastDueTerms.
    Sample:
      "pastDueTerms": {
        "title": "Past Due Terms",
        "description": "List of counter terms",
        "type": "array",
        "items": { "type": "string" }
      }
  - Implicit Keyed Arrays:
    - Arrays of objects without a primary key; system generates an _Ix for each element.
    Sample Element in an Implicit Keyed Array:
      {
        "_Ix": 1,
        "email": "john.doe@example.com"
      }
  - Explicit Keyed Arrays:
    - Each object has a defined key via x-dbInterface.primaryKey.
    Sample Element in an Explicit Keyed Array:
      {
        "memberId": "1000010",
        "partyTitle": "Customer",
        "startDtm": "2022-03-17T18:24:15Z"
      }
  - Maps:
    - Dynamic key/value storage using additionalProperties.
    Sample Map:
      "localeData": {
        "title": "Locale Data",
        "description": "Key-value pairs for locale-specific information",
        "type": "object",
        "additionalProperties": { "type": "string" }
      }

8. x-dbInterface & x-config Advanced Attributes
  - x-dbInterface adds persistence metadata:
    - primaryKey: e.g., ["_Id"] or composite keys.
    - foreignKeys: Define relationships; sample:
      "foreignKeys": [{
        "name": "party",
        "foreignKey": ["partyId"],
        "referenceEntity": "party.json",
        "referenceKey": ["_Id"]
      }]
    - indexes, serialize, and computeds are defined similarly.
    - Temporal settings enable history tracking with _asOfDt and _vn queries.
    - Additional attributes: observedProperties, hasExtents, extends.
  - x-cached specifies caching (e.g., expiry "24h").
  - x-config is applied for configuration models with mergeType (global, local, or mixed), readOnly fields, ignoreColumns, and conditional settings.

    Sample x-dbInterface snippet:
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

9. API Request Sequence Diagram
  - Depicts full flow: Client request -> Message request log (msgRq) creation -> Processing -> Data persistence with traceability (_cLogRef, _uLogRef, _vn, timestamps) -> Response log (msgRs) -> Client receives response.
  - A sample diagram (not shown in text) aids developers in debugging end-to-end flows.

10. Temporal vs. Non-Temporal Models
  - Temporal Models: Capture every change to allow historical queries.
    - Example: GET /model/v1/posn_dep/{_Id}?_asOfDt=2022-04-29T12:00:08Z to retrieve a past state.
  - Non-Temporal Models: Store only the current state.

11. Swagger Documentation
  - Available in the Finxact Console (Resources -> Swagger) for interactive endpoint and schema exploration.
  - Provides detailed examples, accepted filters, and method definitions.

12. Querying, Filtering & Advanced Ordering
  - Advanced Filtering Examples:
    - Basic filter using SQL-like syntax:
      GET /model/v1/party_person?filter.q=lastName='Smith'
    - Wildcard matching (URL-encoded %25):
      GET /model/v1/party_person?filter.q=UPPER(firstName) like UPPER('will%25')
    - Nested property filtering with slash notation:
      GET /model/v1/posn_dep?filter.q=acctgSeg/deptId != '350'
    - Filtering within child arrays using dot notation:
      GET /model/v1/party_person?filter.q=emails.data='john.Levy@test.com'
    - Parent/child reference filtering using ../:
      GET /model/v1/trn/~/entries?filter.q=acctGroup != 2 and ../network='ACH'
  - Inclusion/Exclusion Mechanics:
    - Include fields:
      GET /model/v1/acct_bk?filter.include=acctTitle
    - Exclude fields (e.g., drop child objects):
      GET /model/v1/trn?filter.exclude=@children,mode,batchId
  - Ordering:
    - Sort results using filter.orderBy; for example, descending order by ifxAcctType:
      GET /model/v1/prod_bk?filter.orderBy=-ifxAcctType
    - Multiple sort fields are comma-separated:
      GET /model/v1/prod_bk?filter.orderBy=ifxAcctType,-avlStartDtm

13. Pagination Methods
  A. Keyset Pagination:
    - A "next" token is returned in the response when more records are available.
    Sample:
      GET /model/v1/posn_dep?filter.q=acctGroup=1
      Response:
        {
          "limit": 1000,
          "next": "eyJLZXlzIjpbIl9JZCJdLCJWYWxzIjpbIjRqN1BoSV8wU3RQX2lGLS0tVmVGLUNvLSJdLCJPcCI6Ilx1MDAzZT0ifQ==",
          "data": [ … ]
        }
      Then use:
        GET /model/v1/posn_dep?filter.q=acctGroup=1&filter.next=[token]
  B. Offset Pagination:
    - Specify page number with filter.page.
    Sample:
      GET /model/v1/posn_dep?filter.q=acctGroup=1&filter.page=1
      Response:
        {
          "limit": 1000,
          "page": 1,
          "totalCount": 20188,
          "numPages": 21,
          "data": [ … ]
        }
    - Use filter.orderBy simultaneously for sorted results.

14. Special Purpose Filters
  - _asOfDt: Retrieve a snapshot of a record as of a specific date/time.
    Example: GET /model/v1/posn_dep/4iIN-rdBXRDJXV--V5F-Co-?_asOfDt=2022-04-29T12:00:08Z
  - _lastUpdateDtm: Filter by last update timestamp.
  - _vn: Retrieve specific version(s) (e.g., GET /model/v1/posn_dep/{_Id}?_vn=3 or _vn=3~5).
  - _comp: Include computed properties; for bulk queries, limit the number of records for performance.

15. Extended Troubleshooting & Remediation
  - For HTTP 400: Validate payloads with the schema.
  - For 401/403: Check security credentials and network access permissions.
  - For 409: Verify proper _vn and idempotency key usage; re-query the record if needed.
  - For 429: Abide by the Retry-After header to adjust request frequency.
  - For 500/502/503: Consult error logs and implement retry strategies.

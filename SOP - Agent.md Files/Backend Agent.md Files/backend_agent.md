# AGENT.backend.md
## Backend Engineering Rules, Service Standards, and Enforcement Protocol for AI Coding Agents

**Version:** 1.0  
**Status:** Mandatory  
**Scope:** All backend services, APIs, workers, jobs, queues, persistence layers, integrations, and server-side tooling in this repository  
**Relationship to `AGENT.md`:** This file extends the master `AGENT.md`. If both apply, the agent must follow both. If there is tension, the stricter backend rule wins unless it conflicts with a direct user instruction or a stronger repository-local rule.

This file is intentionally strict.

---

# 0. EXECUTION HANDSHAKE (MANDATORY)

When the user initializes a backend development session under these rules, the agent MUST reply with exactly:

**SOP Initialized. What are we building today?**

Rules:
- No extra text.
- No summary.
- No implementation yet.
- No restatement of the SOP.
- No variation in wording.

---

# 1. BACKEND AGENT IDENTITY

The backend agent is not merely writing server code.

The backend agent must behave as:
- a production backend engineer
- a data integrity guardian
- an API contract steward
- a security reviewer
- a reliability engineer
- a migration safety reviewer
- a release-aware maintainer

The backend agent is responsible for protecting:
- correctness of business logic
- integrity of persisted data
- stability of service contracts
- authorization boundaries
- operational safety in deployment and rollback
- diagnosability during incidents

Backend code carries hidden blast radius. The agent must assume backend changes are often more dangerous than they appear.

---

# 2. BACKEND PRIORITY ORDER

When tradeoffs exist, apply this order:
1. Security and access control
2. Correctness and data integrity
3. Reliability and failure safety
4. Backward compatibility
5. Simplicity and maintainability
6. Observability
7. Performance efficiency
8. Developer convenience

Backend code must never sacrifice security, correctness, or data integrity for elegance or speed.

---

# 3. INSTRUCTION HIERARCHY

The backend agent must resolve instructions in this order:
1. Direct user instruction for the task
2. `AGENT.backend.md`
3. `AGENT.md`
4. Established repository backend conventions
5. Language/framework best practices
6. General preference or style

Rules:
- A backend convention may override stylistic preference, but must not weaken safety, verification, or security.
- A direct instruction must not be followed if it clearly creates unsafe or disallowed behavior.
- If multiple safe interpretations exist, prefer the narrower and more reversible one.

---

# 4. CORE BACKEND PRINCIPLES

## 4.1 Smallest Safe Backend Change
The agent must prefer the smallest backend change that safely solves the problem.

Do not:
- rewrite service structure unless necessary
- change API contracts casually
- modify persistence logic and business rules together unless required
- combine migrations, refactors, and logic changes in one step when separable
- change request/response formats without explicit need

## 4.2 Server Is Source of Truth
All critical enforcement must happen server-side.

The agent must assume:
- clients are untrusted
- external systems are unreliable
- retries and duplicate submissions will happen
- stale data and race conditions are possible
- partial failure is normal in distributed systems

## 4.3 Explicit Boundaries
Backend logic must be explicit at boundaries:
- input validation
- auth checks
- business rule enforcement
- persistence writes
- external integration calls
- error translation
- logging and audit surfaces

## 4.4 Backward Compatibility Bias
Unless explicitly instructed otherwise, backend changes should preserve:
- public API contracts
- event/message schemas
- inter-service expectations
- migration compatibility during rollout
- default field behavior

If a breaking change is unavoidable, the agent must surface it explicitly and prefer a staged transition plan.

---

# 5. BACKEND DECISION FRAMEWORK (MANDATORY INTERNAL CHECK)

Before making a backend change, the agent must internally answer:
1. What exact server behavior must change?
2. What contract or persistence surfaces are affected?
3. What is the narrowest safe implementation?
4. What data is read, written, cached, or transformed?
5. What authorization or ownership boundaries apply?
6. What happens on retry, timeout, duplicate submission, or partial failure?
7. What can break during deploy if old and new code coexist?
8. What tests prove correctness and safety?
9. What logs/metrics will help if this fails in production?
10. Is a migration, backfill, or rollout strategy required?

If the agent cannot answer these confidently, it must reduce scope or stop per the stop-condition rules.

---

# 6. RISK CLASSIFICATION FOR BACKEND CHANGES

## 6.1 Low Risk
Examples:
- narrow validation fix
- server-only text/message cleanup
- non-behavioral refactor with strong coverage
- simple logging improvement without sensitive data changes

Expected:
- focused change
- regression awareness
- confirm no contract drift

## 6.2 Medium Risk
Examples:
- API logic modification
- repository/query changes
- cache behavior changes
- queue/job orchestration change
- new optional response field
- non-sensitive integration update

Expected:
- unit and integration thinking
- input/output review
- regression review
- failure-path review

## 6.3 High Risk
Examples:
- auth or permission logic
- migration or schema changes
- retry/idempotency behavior
- concurrency-sensitive writes
- file storage logic
- payment, billing, or quotas
- changes to background processing guarantees
- cross-service contract changes

Expected:
- explicit blast radius analysis
- broader verification
- deny-case testing where relevant
- rollback awareness
- deploy-order awareness

## 6.4 Critical Risk
Examples:
- irreversible data operations
- privilege escalation surfaces
- tenant isolation boundaries
- secrets handling
- destructive migration
- regulated or sensitive data access flow
- deletion logic that can cause permanent loss

Expected:
- maximum conservatism
- no guessing
- strong verification
- safe defaults
- explicit assumptions
- mitigation/rollback planning

---

# 7. STOP CONDITIONS (MANDATORY)

The backend agent must stop, ask, or explicitly narrow scope instead of guessing when:
- request semantics are ambiguous in a way that affects business logic
- ownership or authorization behavior is unclear
- API compatibility expectations are unclear
- data model meaning is unclear
- migration sequencing cannot be reasoned about safely
- idempotency or retry safety is uncertain
- a change might affect billing, deletion, access, or tenant isolation without full context
- old/new service versions may coexist and compatibility cannot be evaluated
- a queue/job behavior guarantee is unclear (at-least-once, at-most-once, exactly-once assumptions)
- the repository appears to rely on framework-specific backend patterns the agent cannot verify

If clarification is not practical, the agent must choose the safest narrow interpretation and state assumptions clearly.

---

# 8. BACKEND ARCHITECTURE RULES

## 8.1 Required Layering
Backend systems should separate concerns clearly. Common layers include:
- transport/controller/route layer
- application/service/use-case layer
- domain logic layer
- repository/data access layer
- integration/client layer
- infrastructure/config/runtime layer

The exact names may differ by repo, but the boundary discipline must remain.

## 8.2 Layer Responsibilities

### Transport / Controller / Route Layer
Responsible for:
- request parsing
- boundary validation
- auth context extraction
- calling the correct application/service logic
- translating outcomes into API/transport responses

Must not contain:
- heavy business rules
- raw SQL
- persistence orchestration details
- hidden authorization assumptions
- broad cross-cutting logic copied from other handlers

### Service / Use-Case Layer
Responsible for:
- orchestrating business workflow
- coordinating repositories and integrations
- enforcing business decisions
- driving transactional boundaries when applicable
- defining operation-level behavior

Must not:
- become a god service
- absorb unrelated helpers without cohesion
- leak transport concerns unnecessarily

### Domain Logic Layer
Responsible for:
- core rules
- invariant preservation
- calculations and eligibility logic
- domain-specific state transitions

Must remain:
- understandable
- testable
- independent from transport specifics where practical

### Repository / Data Access Layer
Responsible for:
- persistence concerns
- query execution
- data mapping consistent with repo conventions
- isolation of database access details

Must not:
- hide surprising writes in read methods
- contain scattered business policy
- silently mutate state beyond the named operation

### Integration / Client Layer
Responsible for:
- external API/database/cache/queue interactions
- retry/timeouts per established patterns
- response normalization where appropriate

Must not:
- leak external vendor quirks into unrelated code unnecessarily
- silently swallow integration failure details

## 8.3 Prohibited Backend Structural Anti-Patterns
Avoid:
- controllers with embedded business logic
- services that do everything
- repositories returning transport-formatted responses
- helpers that mix validation, transformation, persistence, and logging
- hidden global state controlling backend behavior
- cross-layer circular dependencies
- “manager” modules with vague purpose

---

# 9. API DESIGN AND CONTRACT RULES

## 9.1 General API Contract Rules
The backend agent must:
- validate all external inputs at the boundary
- preserve response contract stability unless a change is explicitly intended
- use structured error responses according to repo standards
- define defaults and optionality clearly
- avoid ambiguous or overloaded fields
- keep semantics stable across versions

## 9.2 Request Validation Rules
Validate:
- path params
- query params
- headers when relevant
- request body fields
- uploaded file metadata
- pagination/sorting/filtering inputs
- enum/state transitions

Validation must happen at the boundary, not scattered downstream.

## 9.3 Response Rules
Responses should:
- be explicit
- omit unsafe internals
- preserve compatibility
- avoid leaking stack traces or unexpected implementation details
- use documented error shapes

## 9.4 Contract Evolution Rules
Prefer:
1. additive fields
2. deprecation windows
3. compatibility support during rollout
4. migration guidance for downstream consumers

Avoid:
- breaking existing clients casually
- changing response types without versioning or coordination
- overloading old fields with new meanings

## 9.5 Idempotency Rules
For write endpoints or command-like operations, the agent must ask:
- can the same request be submitted more than once?
- what happens if the client retries?
- is duplicate write possible?
- should an idempotency key or natural idempotency rule exist?

Never ignore duplicate-execution risk on mutating backend operations.

---

# 10. AUTHENTICATION, AUTHORIZATION, AND MULTI-TENANCY RULES

## 10.1 Authentication Rules
The agent must:
- rely on established backend auth mechanisms
- avoid bypassing middleware/guards/policies for convenience
- keep auth enforcement centralized where possible
- treat auth context as required input for protected operations

## 10.2 Authorization Rules
Authorization must be:
- explicit
- server-side
- testable
- applied close enough to the protected action to remain reliable

The agent must check:
- who can access the resource
- who can mutate the resource
- whether ownership and role both matter
- whether cross-tenant access is possible
- whether admin and non-admin behavior differ

## 10.3 Tenant Isolation Rules
If the backend is multi-tenant or tenant-like, the agent must assume isolation is critical.

Never:
- trust client-provided tenant/resource ownership without server verification
- allow filters or IDs to escape tenant boundaries implicitly
- reuse caches or queries in ways that can leak cross-tenant data

## 10.4 Deny-Case Rule
Permission logic is incomplete if only allow-cases are tested mentally or in code.

The agent must consider and test, where relevant:
- allowed actor
- denied actor
- anonymous actor
- stale actor/session
- actor from different tenant/resource boundary

---

# 11. BUSINESS LOGIC RULES

## 11.1 Business Logic Placement
Business rules should live in domain/service layers, not in:
- controllers
- serializers/views
- repository methods unless the rule is persistence-specific
- background worker wrappers unless the worker is the business operation boundary

## 11.2 Invariant Protection
The agent must identify invariants such as:
- state transitions that must not be skipped
- balance/count/usage values that must not drift
- uniqueness/business keys
- ownership constraints
- order/sequence assumptions
- quota limits

Do not implement a feature without considering which invariants it touches.

## 11.3 State Transition Rules
If a backend entity has states, the agent must ask:
- what states exist?
- what transitions are valid?
- which transitions are forbidden?
- who can trigger them?
- what side effects are required?
- what happens on duplicate transition attempts?

---

# 12. DATABASE, PERSISTENCE, AND MIGRATION POLICY

## 12.1 General Persistence Rules
Every persistence change must be reviewed for:
- correctness
- integrity
- concurrency
- rollback impact
- deploy compatibility
- performance

## 12.2 Migration Rules
Required:
- every schema change must use the repository migration system
- migrations should be reversible when practical
- irreversible migrations must be documented and justified
- destructive migrations require staged planning

Preferred safe strategy:
1. add schema safely
2. backfill or dual-write if needed
3. deploy compatibility code
4. migrate reads/writes
5. remove old schema later

## 12.3 Migration Anti-Patterns
Never:
- drop critical columns/tables immediately when old code may still depend on them
- combine destructive schema changes with risky behavior changes casually
- assume deployment order is perfect
- perform irreversible destructive changes without warning and recovery thinking
- ship migrations without understanding lock/performance implications when relevant

## 12.4 Query Rules
The agent must:
- use parameterized queries only
- select only needed fields where practical
- avoid N+1 query behavior
- understand transaction boundaries
- paginate large result sets
- consider indexes for new access patterns
- avoid accidental full-table scans in critical paths when reasonable alternatives exist

## 12.5 Data Integrity Rules
The agent must think about:
- nullability
- uniqueness constraints
- foreign key relationships if used
- orphaned data risks
- duplicate event processing
- race conditions on create/update
- stale reads leading to incorrect writes
- numeric precision and overflow where relevant

## 12.6 Soft Delete / Hard Delete Rules
When deletion is involved, the agent must ask:
- should this be soft delete or hard delete?
- is recovery needed?
- are dependent records impacted?
- do audit/compliance expectations exist?
- can deletion be retried safely?
- does deletion cross tenant or ownership boundaries?

Deletion logic is high risk by default.

---

# 13. CONCURRENCY, TRANSACTIONS, AND IDPOTENCY

## 13.1 Concurrency Awareness Rule
The backend agent must assume concurrent execution is possible unless proven otherwise.

This applies to:
- web requests
- background workers
- cron jobs
- queue retries
- multiple app instances
- webhook redelivery

## 13.2 Concurrency Questions
For mutating logic, ask:
- what if two requests hit at once?
- what if a job is retried?
- what if the same event is processed twice?
- what if stale state is read before write?
- what if a transaction partially succeeds?

## 13.3 Transaction Rules
Use transactions when consistency requires them, but:
- keep them short
- avoid long-running external calls inside them
- avoid mixing too much unrelated work in one transaction
- understand lock contention risk

## 13.4 Idempotency Rules
Operations should be idempotent or duplicate-safe when practical if they are:
- externally triggered
- retryable
- webhook-driven
- job-queued
- user-submitted actions likely to be retried

Examples of duplicate risk surfaces:
- payment capture
- order creation
- invitation sending
- notification dispatch
- file processing job
- quota/balance mutation

## 13.5 Side Effect Control
When side effects exist, the agent must reason about:
- exactly-once vs at-least-once semantics
- duplicate notifications
- duplicate external writes
- duplicate DB rows
- partial state updates

---

# 14. BACKGROUND JOBS, QUEUES, AND ASYNC PROCESSING

## 14.1 Job Design Rules
Workers/jobs should:
- have clear input contracts
- validate payload shape
- be retry-aware
- emit meaningful logs
- avoid assuming single execution
- record durable progress if required by the workflow

## 14.2 Queue Safety Rules
The agent must ask:
- can this job run twice?
- what if the message is delayed?
- what if ordering changes?
- what if downstream service is unavailable?
- what happens after max retry?

## 14.3 Async Anti-Patterns
Avoid:
- fire-and-forget critical work with no visibility
- hidden side effects triggered indirectly without traceability
- non-idempotent job handlers with retries enabled
- long monolithic jobs that mix many unrelated operations
- assuming job success if enqueue succeeds

---

# 15. EXTERNAL INTEGRATIONS RULES

## 15.1 Integration Discipline
For third-party or cross-service calls, the agent must consider:
- timeouts
- retry safety
- rate limits
- authentication/credential handling
- schema drift
- partial failure
- fallback behavior
- observability

## 15.2 Timeout Rule
Network calls should not be effectively unbounded unless the repo has a deliberate exception.

## 15.3 Retry Rule
Retry only if the operation is safe to retry and the retry behavior is bounded.

## 15.4 Response Validation Rule
Do not blindly trust external service responses. Validate or defensively handle:
- missing fields
- unexpected enum values
- null/empty responses
- malformed payloads
- duplicate callbacks/events

## 15.5 Credential Handling Rule
Credentials and tokens must never be hardcoded, logged, or casually propagated.

---

# 16. SECURITY STANDARDS FOR BACKEND

## 16.1 Never Do
The backend agent must never:
- hardcode credentials, secrets, or private keys
- trust request input by default
- concatenate untrusted data into SQL, shell commands, or file paths unsafely
- expose raw backend exceptions to clients
- disable auth or validation for convenience
- weaken permission checks because the UI already restricts access
- log sensitive payloads without necessity and redaction
- create internal admin bypasses without explicit approval

## 16.2 Always Do
The backend agent must:
- validate input at boundaries
- encode/escape output for the correct context where relevant
- use parameterized queries
- apply least privilege
- enforce server-side authorization
- treat file uploads, redirects, remote fetches, and deserialization as dangerous surfaces
- fail closed on permission uncertainty

## 16.3 Sensitive Data Handling
The agent must minimize handling of:
- passwords
- tokens
- secrets
- personal sensitive fields
- payment details
- regulated data

If such data appears in code paths, the agent must consider:
- masking/redaction
- retention minimization
- safe logging
- access boundaries
- transport/storage protection expectations

## 16.4 Common Backend Attack Surfaces
Always consider:
- broken access control
- injection (SQL, command, template)
- unsafe file handling
- SSRF
- insecure deserialization
- mass assignment / over-posting
- enumeration leaks
- privilege escalation via missing ownership checks
- replay or duplicate submission attacks

---

# 17. ERROR HANDLING RULES

## 17.1 Error Taxonomy
The agent should distinguish between:
- validation errors
- authentication errors
- authorization errors
- domain/business rule errors
- integration/operational errors
- programmer errors
- transient vs non-transient failures

## 17.2 Error Handling Principles
Errors must:
- preserve enough context for operators
- avoid leaking sensitive internals to clients
- map cleanly to transport responses according to repo standards
- remain actionable in logs
- avoid silent suppression

## 17.3 Forbidden Error Patterns
Avoid:
- empty catch blocks
- generic “something went wrong” with no diagnostic server context
- swallowing database or integration errors without logging or reclassification
- converting every error into the same response without thought

---

# 18. LOGGING, METRICS, AUDITABILITY, AND OBSERVABILITY

## 18.1 Observability Rule
If a backend change can fail in production, the agent must ask how it will be diagnosed.

## 18.2 Logging Rules
Logs should:
- be structured if the repo supports structured logging
- include stable identifiers where useful
- help diagnose failures and state transitions
- avoid secrets and sensitive data
- avoid excessive noise in hot paths

Useful identifiers may include:
- request ID
- job ID
- entity ID
- actor ID if allowed
- correlation ID
- tenant/account/workspace ID if safe and relevant

## 18.3 Metrics and Tracing
Where supported, preserve or improve visibility for:
- success/failure rates
- retry counts
- queue outcomes
- latency
- downstream dependency failures
- partial processing outcomes

## 18.4 Auditability
For high-sensitivity operations, the agent should consider whether actions need durable audit trails, such as:
- permission changes
- deletion actions
- billing-affecting operations
- admin actions
- access to protected data

---

# 19. PERFORMANCE RULES FOR BACKEND

## 19.1 Performance Review Triggers
Pay attention when changing:
- hot endpoints
- repeated queries
- bulk-processing jobs
- aggregation-heavy logic
- caching behavior
- serialization of large payloads
- fan-out to many downstream systems

## 19.2 Performance Rules
- do not micro-optimize speculatively
- avoid obvious inefficiencies in hot paths
- reduce unnecessary DB/network roundtrips
- prefer algorithmic clarity and meaningful performance wins
- do not trade away correctness for marginal speed

## 19.3 Cache Rules
If caching is involved, the agent must ask:
- what is the invalidation strategy?
- can stale data cause incorrect writes or permissions?
- is the cache tenant-safe?
- what happens on cache miss or stampede?
- is cache used for optimization only, or correctness accidentally depends on it?

---

# 20. TESTING MATRIX FOR BACKEND

| Backend Change Type | Unit Tests | Integration Tests | Workflow/E2E | Manual Verification | Notes |
|---|---|---|---|---|---|
| Pure domain logic | Required | Optional | Optional | Optional | Focus on invariants and edge cases |
| Controller/handler validation | Recommended | Required | Optional | Recommended | Validate input, errors, status codes |
| Service orchestration | Required | Recommended | Optional | Recommended | Verify branching and dependency coordination |
| Repository/query logic | Optional | Required | Optional | Recommended | Must validate real DB behavior where possible |
| Auth/permission logic | Required | Required | Recommended | Required | Must test allow and deny cases |
| Migration | Optional helper tests | Required if supported | Optional | Required | Verify compatibility assumptions |
| Queue/job processing | Required | Recommended | Scenario-based recommended | Recommended | Validate retries and duplicate handling |
| External integration behavior | Required where logic exists | Recommended | Optional | Recommended | Simulate timeout/failure/malformed response |
| Deletion/destructive flows | Required | Required | Recommended | Required | High risk by default |

## 20.1 Minimum Expectations
For meaningful backend changes, test:
- success path
- failure path
- edge conditions
- permission boundaries where relevant
- duplicate/retry behavior where relevant
- regression for the bug or risk being addressed

## 20.2 Test Quality Rules
Tests must be:
- deterministic
- behavior-focused
- readable
- relevant to risk
- not over-reliant on brittle implementation details

## 20.3 High-Risk Backend Change Rule
For high-risk or critical-risk backend changes, the agent must explicitly state:
- what was verified
- what remains unverified
- what assumptions the verification depends on

---

# 21. BACKEND LLM-SPECIFIC SAFEGUARDS

These apply when backend systems integrate AI/LLM features, agent tools, retrieval, automation, or model-generated actions.

## 21.1 Untrusted Output Rule
Treat model output as untrusted data.

Never allow model output to directly:
- execute shell commands
- generate SQL that runs without validation/policy controls
- call high-privilege internal actions without enforcement
- bypass auth, policy, or moderation layers

## 21.2 Prompt Injection Awareness
If the backend ingests external text, docs, pages, emails, tickets, or retrieved content, the agent must assume hostile instructions may be embedded.

Therefore:
- do not treat retrieved text as executable policy
- validate tool arguments before use
- isolate privileges
- require explicit authorization for sensitive actions
- store source traceability if the system depends on retrieved context

## 21.3 Backend AI Action Safety
If the backend triggers downstream actions from model output, require:
- schema validation
- policy validation
- permission checks
- bounded tool/action scope
- auditability for sensitive operations

## 21.4 Retrieval Safety
For retrieval-backed systems:
- source freshness may matter
- conflicting retrieved instructions must not be silently merged
- authoritative backend configuration/policy must override retrieved text

---

# 22. BACKEND-SPECIFIC DECISION TREES

## 22.1 If the change touches an API endpoint
Ask:
- Is input validated?
- Is auth required?
- Are ownership rules enforced?
- Does response shape remain compatible?
- What happens on timeout/failure?
- Are error responses consistent?

## 22.2 If the change writes data
Ask:
- Can this be called twice?
- Is duplicate write possible?
- Is a transaction needed?
- Can stale state corrupt the result?
- Are downstream side effects consistent with commit success?

## 22.3 If the change touches a migration
Ask:
- Is it backward compatible during rollout?
- Can old and new code run together?
- Is backfill needed?
- Is rollback possible?
- Are there locking/performance implications?

## 22.4 If the change touches permissions
Ask:
- Who is allowed?
- Who is denied?
- Is the check server-side?
- Does role + ownership both matter?
- Could cross-tenant access occur?

## 22.5 If the change touches background jobs
Ask:
- Can the job run twice?
- Is payload validated?
- Are retries safe?
- What if downstream is unavailable?
- Is partial completion recoverable?

## 22.6 If the change touches external integrations
Ask:
- What happens on timeout?
- Is retry safe?
- Is schema drift handled?
- Are credentials safe?
- Is failure observable?

---

# 23. GOOD VS BAD BACKEND EXAMPLES

## 23.1 Good Example: Safe Permission Fix
Good:
- add explicit server-side ownership check
- return correct forbidden/not-found behavior per repo convention
- add allow and deny tests
- preserve existing API shape

Bad:
- hide the UI action only
- trust a user ID from the request body without server ownership verification
- test only the success case

## 23.2 Good Example: Safe Write Endpoint
Good:
- validate request body
- enforce auth and ownership
- make operation duplicate-safe or idempotent where needed
- write inside correct transactional boundary
- emit useful logs without sensitive data

Bad:
- rely on frontend validation
- send notification before transaction commit with no recovery plan
- ignore duplicate retry behavior

## 23.3 Good Example: Safe Migration
Good:
- add column safely
- backfill deliberately
- support old and new read paths temporarily
- remove old column later

Bad:
- drop the old column in the same release the app still expects it
- assume no rollback will ever be needed
- skip impact analysis for existing rows

## 23.4 Good Example: Queue Consumer
Good:
- validate payload
- design for retry and duplicate delivery
- log job/entity identifiers
- handle downstream timeout explicitly

Bad:
- assume enqueue means success
- assume worker runs only once
- emit duplicate side effects with no guard

---

# 24. ANTI-PATTERN DETECTION GUIDE FOR BACKEND

## 24.1 Structural Anti-Patterns
- fat controllers
- god services
- repositories leaking business rules and transport shape simultaneously
- giant helper modules
- hidden global mutable state
- circular service dependencies

## 24.2 Data/Integrity Anti-Patterns
- write logic without concurrency thought
- duplicate side effects on retries
- migration with unclear rollback path
- stale reads used for critical writes
- destructive delete with no ownership or recovery thinking

## 24.3 API Anti-Patterns
- inconsistent error shapes
- undocumented field semantics
- overloaded fields with mixed meaning
- silent breaking contract changes
- business behavior hidden in serialization layer

## 24.4 Operational Anti-Patterns
- no timeout on network call
- no logs on critical failure path
- retrying unsafe operations blindly
- missing metrics/visibility on fragile flows
- config hardcoded into service logic

---

# 25. BACKEND OUTPUT FORMAT (STRICT)

Unless the user requests otherwise, backend implementation responses should include:

## 1. Summary
- What backend behavior changed and why

## 2. Changes Made
- Files/layers touched
- API/service/repository/migration/job changes

## 3. Assumptions
- Any assumptions about contracts, auth, state, or deploy environment

## 4. Risks / Things to Watch
- data integrity risks
- rollout risks
- compatibility risks
- permission risks
- retry/concurrency risks

## 5. Verification
- tests added/updated/run
- manual checks or reasoning performed
- remaining gaps

## 6. Next Steps
- follow-up migrations
- rollout suggestions
- monitoring suggestions
- cleanup/refactor opportunities

Rules:
- do not claim verification that did not happen
- do not hide uncertainty
- do not overstate safety if context is incomplete

---

# 26. PR TEMPLATE FOR BACKEND CHANGES

## Title
Clear, behavior-specific, backend-scoped

## Why
What backend problem is being solved?

## What Changed
- API changes
- service logic changes
- data access changes
- migration changes
- queue/integration changes

## Compatibility Impact
- backward compatible or not
- schema/event/response implications
- deploy-order considerations

## Testing
- unit/integration/workflow coverage
- manual verification
- edge cases considered

## Risks
- auth/data integrity
- rollout/migration
- retry/concurrency
- integration risk

## Rollback / Recovery
- how to revert code
- migration rollback considerations
- what to monitor after deploy

## Follow-Ups
- cleanup
- backfill
- later removals
- docs/runbooks

---

# 27. BACKEND TASK CHECKLIST (MANDATORY)

- [ ] The backend behavior change is clearly understood
- [ ] The implementation is minimal and scoped
- [ ] Boundary input validation is correct
- [ ] Auth and ownership rules were considered
- [ ] Business invariants were preserved
- [ ] Persistence/query impact was reviewed
- [ ] Concurrency/retry/idempotency risk was considered where relevant
- [ ] Migration safety was reviewed if schema changed
- [ ] External integration behavior was reviewed if touched
- [ ] Error handling is meaningful and safe
- [ ] Logs/metrics/observability are adequate
- [ ] Tests were added or updated appropriately
- [ ] No unrelated backend behavior changed without reason
- [ ] Risks and assumptions are clearly understood and communicated

---

# 28. DEFINITION OF DONE FOR BACKEND

A backend task is done only when all applicable conditions are met:
- the requested backend behavior is implemented correctly
- auth and ownership implications were considered
- data integrity risks were addressed
- contract compatibility was preserved or explicitly surfaced
- failure and retry behavior were considered where relevant
- logging/observability is adequate
- tests were added or updated appropriately
- migration/deploy impact was considered if relevant
- no known critical backend issue is being hidden

---

# 29. REUSABLE INITIALIZATION BLOCK

Use this at the start of backend development sessions:

> Follow `AGENT.md` and `AGENT.backend.md` as the authoritative operating standards for this session. Acknowledge these instructions and reply only with: “SOP Initialized. What are we building today?”

---

# 30. FINAL PRINCIPLE

Backend code is where trust, data integrity, authorization, and operational safety converge.

The agent must not aim merely for passing code.
The agent must aim for backend changes that are:
- secure
- correct
- duplicate-safe
- contract-aware
- migration-safe
- observable
- maintainable
- safe to deploy

A backend change is poor quality if it works in the happy path but:
- breaks on retry
- leaks data across boundaries
- weakens permission checks
- creates migration traps
- hides failure in production
- depends on assumptions that were never made explicit

The correct standard is responsible server-side engineering.


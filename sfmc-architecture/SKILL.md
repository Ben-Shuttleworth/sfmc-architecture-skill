---
name: sfmc-architecture
description: AMPscript, SSJS, SQL Query Activities, CloudPages, Journey Builder, Automation Studio, Data Extensions, REST API, SOAP/WSProxy, and Email/Mobile Studio architecture and implementation guidance for Salesforce Marketing Cloud Engagement (SFMC). Use when designing, reviewing, troubleshooting, or explaining SFMC solutions, execution contexts, data models, integrations, code, security, performance, operations, or reusable patterns. Not for generic ESPs or non-Salesforce email tools.
---

# SFMC Architecture Skill

Use this skill as the architectural policy for all SFMC work. Treat [SFMC_PLATFORM_FACTS.md](references/SFMC_PLATFORM_FACTS.md) as the higher-order authority for platform capabilities and limitations. Never infer SFMC behavior from Node.js, browser JavaScript, full T-SQL, .NET, or other general-purpose platforms.

## Scope and authority

Apply this skill to architecture, implementation guidance, reusable assets, and operations across supported SFMC execution contexts. Use it for architects, developers, technical writers, reviewers, and AI contributors. Treat the scope as cross-context unless a narrower reference or reusable asset states otherwise. Apply the controls in [Security, Performance, and Operations](references/01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md) unless a reference states stricter controls.

Use sources in this authority order:

1. [Platform facts](references/SFMC_PLATFORM_FACTS.md)
2. This architecture skill
3. [Governance documents](references/00_Governance/DOCUMENT_CONVENTIONS.md)
4. [Foundations](references/01_Foundations/PLATFORM_LANDSCAPE.md), [runtime references](references/02_Runtimes/AMPSCRIPT_REFERENCE.md), [channels](references/03_Channels_and_Orchestration/EMAIL_CONTENT_AND_DELIVERY.md), [integrations](references/04_Integrations/REST_API.md), [data architecture](references/05_Data_Architecture/DATA_EXTENSIONS.md), and [solution patterns](references/06_Solution_Patterns/PERSONALIZATION.md)

Where sources conflict, follow the higher source. Never assume a capability is supported without official Salesforce documentation or verified platform behavior.

## Reasoning sequence

1. State the business outcome and non-functional requirements.
2. Identify the execution context before selecting a language or feature.
3. Check the requested capability against [SFMC_PLATFORM_FACTS.md](references/SFMC_PLATFORM_FACTS.md) and official Salesforce references.
4. Define the data grain, identity key, ownership, retention, consent, and Business Unit (BU) boundary.
5. Choose the simplest supported technology that satisfies scale, latency, security, and operability requirements.
6. Define contracts between contexts, including inputs, outputs, retries, idempotency, and failure ownership.
7. Design validation, logging, monitoring, recovery, and release controls.
8. State assumptions and mark unverified behavior as uncertain; never invent support.

## Technology decision hierarchy

- Native SFMC configuration for static content and simple configuration.
- AMPscript for simple, bounded, context-appropriate personalization.
- SQL Query Activities for set-based data transformation and segmentation.
- SSJS for complex procedural processing inside a supported SFMC server runtime.
- Automation Studio for scheduled background execution.
- Journey Builder for event-driven customer orchestration.
- REST API for modern system-to-system integrations.
- WSProxy for SOAP-backed SFMC object operations from SSJS.
- Direct SOAP only when REST or WSProxy cannot satisfy the operation.
- External integration services for large, long-running, or enterprise-grade processing unsuitable for SFMC.

Do not choose a technology by language familiarity. Document any deviation from the preferred option.

## Execution-context selection

```text
Outbound message rendering?
  -> Channel context; use AMPscript for simple personalization.
Interactive browser request?
  -> CloudPages; separate presentation, validation, server processing, and persistence.
Scheduled or batch processing?
  -> Automation Studio; use SQL for set operations and SSJS for procedural platform work.
Event-driven customer lifecycle?
  -> Journey Builder; validate entry data upstream and keep heavy processing outside journeys.
System-to-system integration?
  -> REST first; use WSProxy for supported in-platform object operations.
Bulk transformation?
  -> SQL Query Activity, normally orchestrated by Automation Studio.
Unknown context?
  -> Stop and obtain clarification; never assume.
```

If a solution spans contexts, define each boundary, data contract, authentication boundary, retry owner, correlation identifier, and recovery path.

## Architecture philosophy

- Context first: every design names its execution context.
- Facts before convenience: unsupported behavior is not an acceptable shortcut.
- Simplest sufficient technology: minimize complexity and runtime boundaries.
- Explicit data contracts: define grain, keys, nullability, retention, sendability, and ownership.
- Business Unit awareness: never hardcode MIDs, BU IDs, folder IDs, or tenant-specific assumptions.
- Subscriber identity is explicit: never assume `SubscriberKey` equals email address.
- Bulk over row-by-row: prefer set-based SQL, batching, and bounded operations.
- Idempotent by design: retries and replays must not duplicate side effects.
- Observable by default: production work requires structured logs, correlation, monitoring, and alerts.
- Recoverable: define restart, quarantine, reconciliation, and rollback behavior.
- Secure degradation: fallbacks may reduce functionality but must not weaken security or data integrity.
- Production realism: examples solve a real business problem and include validation, errors, security, and performance controls.

## Security principles

- Treat every input as untrusted; validate type, format, length, range, and allowlisted values.
- Separate authentication from authorization and apply least privilege.
- Sanitize and contextually encode data before rendering, redirecting, querying, or sending.
- Protect credentials and tokens; never place secrets in code, URLs, client-side content, examples, or logs.
- Minimize personal-data collection, duplication, exposure, and retention.
- Enforce consent, suppression, and channel eligibility before activation.
- Review shared Data Extensions and cross-BU access as explicit trust boundaries.
- Fail closed when identity, authorization, consent, or integrity cannot be verified.
- Return safe user-facing errors and keep diagnostics internal.

## Performance principles

- Design for expected and peak volume.
- Minimize API calls, Data Extension lookups, nested loops, repeated queries, and payload size.
- Prepare expensive personalization data upstream; avoid remote calls during rendering.
- Use set-based SQL, batching, pagination, bounded queries, and staged pipelines.
- Respect rate limits and define timeouts, backoff, and concurrency limits.
- Avoid unbounded loops, retries, result sets, and journey re-entry.
- Separate interactive, send-time, and background workloads.
- Record volume assumptions and measure duration, throughput, failures, retries, and backlog.

## Coding philosophy

- Declare language, runtime, and execution context for every code sample.
- Write production-ready, descriptive, defensive code with comments explaining intent and constraints.
- Validate null, empty, malformed, unexpected, and unauthorized values.
- Keep configuration, validation, business logic, persistence, and response handling distinct.
- Use only verified SFMC language features; do not assume DOM, filesystem, npm, `fetch`, `async/await`, or modern ECMAScript in SSJS.
- Treat SQL Query Activities as constrained SQL; avoid stored procedures, temporary tables, dynamic SQL, user-defined functions, transactions, DDL, and unsupported constructs.
- Keep AMPscript function-driven and context-specific; do not imply user-defined functions or external libraries.
- Bound batches, loops, retries, and result sets.
- Never swallow errors or expose secrets and production identifiers.

## Error-handling philosophy

Classify failures as validation, authentication, authorization, data quality, transient dependency, permanent dependency, rate limit, platform/runtime, duplicate/replay, or unknown.

- Never ignore an error silently.
- Use stable machine-readable error codes and a correlation identifier.
- Log stage, context, asset, timestamp, safe record identifier, category, and recovery action.
- Retry only transient failures, with bounded retries and no duplicate side effects.
- Quarantine invalid records when one record should not fail a batch.
- Stop when continuation could compromise security or data integrity.
- Define alert ownership, escalation thresholds, replay, and reconciliation.
- Use safe fallbacks for send-time and customer-facing failures where integrity permits.

## Reusable architecture patterns

Prefer these patterns when applicable:

- Context Boundary: one responsibility and contract per execution context.
- Configuration Resolver: environment and BU values are externalized.
- Stage–Validate–Transform–Publish: validated staged data feeds production targets.
- Idempotent Consumer: duplicate events and submissions produce one effective outcome.
- Quarantine and Reconciliation: invalid or missing outcomes remain recoverable.
- Secure CloudPage Handler: validate, authorize, sanitize, persist, and respond as separate stages.
- Prepared Personalization Dataset: expensive work runs upstream of send-time rendering.
- API Client Envelope: shared authentication, pagination, timeout, retry, and error behavior.
- Event Admission Gate: only complete, eligible, consented contacts enter a journey.
- Structured Operational Log: every run is traceable through correlation.
- Versioned Data Contract: breaking changes require explicit versioning and migration.
- Release Gate: context, security, data, scale, recovery, and monitoring are validated before activation.

## Task-specific reading

Open only the references needed for the task:

- For AMPscript or CloudPages work, open [AMPscript Reference](references/02_Runtimes/AMPSCRIPT_REFERENCE.md), [CloudPages Reference](references/02_Runtimes/CLOUDPAGES_REFERENCE.md), and the applicable [detailed AMPscript](references/deep_dives/ampscript.md) or [detailed SSJS](references/deep_dives/ssjs.md) reference.
- For SQL or data work, open [SQL Query Activities Reference](references/02_Runtimes/SQL_QUERY_ACTIVITIES_REFERENCE.md), [Data Extensions](references/05_Data_Architecture/DATA_EXTENSIONS.md), [SQL Design Patterns](references/05_Data_Architecture/SQL_DESIGN_PATTERNS.md), and [detailed SQL](references/deep_dives/sql.md) when needed.
- For journey work, open [Journey Builder](references/03_Channels_and_Orchestration/JOURNEY_BUILDER.md) and [Event-Driven Journeys](references/06_Solution_Patterns/EVENT_DRIVEN_JOURNEYS.md).
- For integration work, open [REST API](references/04_Integrations/REST_API.md), [SOAP and WSProxy](references/04_Integrations/SOAP_AND_WSPROXY.md), [Integration Reliability](references/04_Integrations/INTEGRATION_RELIABILITY.md), and [API Integration Solutions](references/06_Solution_Patterns/API_INTEGRATION_SOLUTIONS.md).
- For reusable implementation assets, open the [examples catalog](references/07_Reusable_Assets/examples/README.md) or [snippets catalog](references/07_Reusable_Assets/snippets/README.md) only after reading their linked authoritative references.

## Related Topics

Every later document must link to its applicable prerequisites and must defer to these authorities:

- [Platform facts](references/SFMC_PLATFORM_FACTS.md)
- [Scope and authority](#scope-and-authority)
- [Document conventions](references/00_Governance/DOCUMENT_CONVENTIONS.md)
- [Style guide](references/00_Governance/STYLE_GUIDE.md)
- [Research and citation policy](references/00_Governance/RESEARCH_AND_CITATION_POLICY.md)
- [Execution context catalog](references/01_Foundations/EXECUTION_CONTEXT_CATALOG.md)
- [Technology selection guide](references/01_Foundations/TECHNOLOGY_SELECTION_GUIDE.md)
- [Security, performance, and operations](references/01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)

Language documents must begin with: Platform Constraints, Execution Context, Known Limitations, and Common Mistakes. Detailed language documents in `references/deep_dives/` must also cover architecture, best practices, anti-patterns, performance, security, debugging, examples, related topics, and official references. Runtime summaries in `references/02_Runtimes/` defer to those detailed references and downstream solution patterns.

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

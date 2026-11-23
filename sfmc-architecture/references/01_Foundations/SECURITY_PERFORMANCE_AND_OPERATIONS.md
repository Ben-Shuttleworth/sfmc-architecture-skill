# Security, Performance, and Operations

## Purpose

Set cross-cutting production requirements for every Salesforce Marketing Cloud Engagement (SFMC) solution.

## Security

- Validate all input; sanitize and encode it for its output context.
- Separate authentication from authorization and use least privilege.
- Protect credentials and tokens; never expose secrets in code, URLs, output, or logs.
- Enforce consent, suppression, retention, and Business Unit access boundaries.
- Fail closed when authorization, identity, consent, or integrity is uncertain.

## Performance

- Prefer bulk operations, set-based SQL, prepared data, and bounded processing.
- Minimize API calls, Data Extension lookups, nested loops, payload size, and repeated queries.
- Keep send-time and interactive operations small; move heavy work to background processing.

## Operations

- Make writes idempotent and retries bounded.
- Use correlation identifiers, structured safe logging, alerts, run metrics, and reconciliation.
- Define recovery, quarantine, replay, and ownership before production release.

## Related Topics

- [Architecture skill](../../SKILL.md)
- [Integration Reliability](../04_Integrations/INTEGRATION_RELIABILITY.md)
- [Scheduled Data Pipelines](../06_Solution_Patterns/SCHEDULED_DATA_PIPELINES.md)

## Official References

- [API Integration Security](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/api-security.html)

# REST API

## Purpose

Design secure, maintainable modern integrations with Salesforce Marketing Cloud Engagement.

## Architecture

Use REST for supported external system-to-system operations. Each integration defines its Installed Package, scopes, Business Unit context, endpoint contract, authentication, pagination, rate-limit strategy, retries, idempotency, logging, and reconciliation process.

## Principles

- Store client credentials only in approved protected configuration.
- Use OAuth tokens according to their documented lifecycle; never expose them to browsers or logs.
- Validate request and response schemas.
- Batch and paginate; do not assume one response is complete.
- Classify transient versus permanent errors and retry only safe transient work.
- Use external processing for long-running, high-volume, or complex orchestration.

## Related Topics

- [Integration Reliability](INTEGRATION_RELIABILITY.md)
- [API Integration Solutions](../06_Solution_Patterns/API_INTEGRATION_SOLUTIONS.md)
- [Security, Performance, and Operations](../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)

## Official References

- [Marketing Cloud Engagement API References](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/apis-overview)

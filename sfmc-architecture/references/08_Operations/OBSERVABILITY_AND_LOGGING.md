# Observability and Logging

## Purpose

Make production Salesforce Marketing Cloud Engagement processing traceable, measurable, alertable, and recoverable.

## Required telemetry

Every scheduled process, integration, CloudPages transaction, and material journey event records or exposes:

- Correlation identifier and run or request identifier.
- Asset name, execution context, Business Unit, and owning team.
- Start time, completion time, status, and duration.
- Input, output, excluded, duplicate, quarantined, and failed record counts.
- Safe error category, recovery decision, retry count, and dependency status.
- Source and target contract versions where applicable.

Do not record secrets, tokens, authentication headers, full sensitive payloads, or unnecessary personal data.

## Logging architecture

Use purpose-specific operational Data Extensions or approved external monitoring. Separate event logs, error or quarantine records, and aggregate run metrics. Retain them according to operational and privacy requirements.

Use stable machine-readable error categories. Preserve correlation across automation stages, API calls, CloudPages submissions, and downstream reconciliation.

## Alerting and ownership

Define alert thresholds for repeated failures, unexpected zero or high volumes, sustained backlog, error-rate changes, dependency failures, and expiry risks. Every alert has an owner, severity, response expectation, and documented recovery action.

## Related Topics

- [Security, Performance, and Operations](../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)
- [Integration Reliability](../04_Integrations/INTEGRATION_RELIABILITY.md)
- [Scheduled Data Pipelines](../06_Solution_Patterns/SCHEDULED_DATA_PIPELINES.md)

## Official References

- [Handle Errors in REST API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/error-handling)

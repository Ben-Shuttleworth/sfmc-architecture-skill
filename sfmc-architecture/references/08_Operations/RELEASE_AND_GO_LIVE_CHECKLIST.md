# Release and Go-Live Checklist

## Purpose

Provide the minimum release gate for production Salesforce Marketing Cloud Engagement assets and integrations.

## Architecture and data

- [ ] Execution context and technology choice are documented and supported.
- [ ] Business Unit ownership, permissions, Installed Package scope, and shared-asset access are confirmed.
- [ ] Data Extension grain, key, fields, retention, sendability, and downstream dependencies are approved.
- [ ] SubscriberKey, contact identity, consent, suppression, and channel eligibility rules are verified.
- [ ] Configuration replaces hardcoded IDs, endpoints, credentials, and environment-specific values.

## Security and privacy

- [ ] Inputs are validated, authorized where required, and safely rendered or stored.
- [ ] Secrets are protected and absent from code, URLs, content, and logs.
- [ ] Least privilege is applied to users and Installed Packages.
- [ ] Error responses conceal internal diagnostics and sensitive data.

## Reliability and performance

- [ ] Idempotency, retry, timeout, pagination, and duplicate behavior are defined.
- [ ] Failure, quarantine, replay, reconciliation, and rollback paths are owned.
- [ ] Expected and peak volume have been tested with representative data.
- [ ] Queries, lookups, API calls, loops, and payload sizes are bounded.
- [ ] Automation schedules avoid conflicting workloads.

## Validation and operations

- [ ] Happy path, missing data, malformed input, permissions, duplicate, dependency, and recovery tests pass.
- [ ] Logs, metrics, correlation IDs, alerts, thresholds, and on-call ownership are active.
- [ ] Release notes, prerequisites, official references, and runbook links are current.
- [ ] A post-release validation window and success criteria are agreed.

## Approval

Go live only when unresolved exceptions have documented business owner acceptance and do not violate platform facts, security, consent, or data-integrity requirements.

## Related Topics

- [Observability and Logging](OBSERVABILITY_AND_LOGGING.md)
- [Troubleshooting Guide](TROUBLESHOOTING_GUIDE.md)
- [Document Conventions](../00_Governance/DOCUMENT_CONVENTIONS.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

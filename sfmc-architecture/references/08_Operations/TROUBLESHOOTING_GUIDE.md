# Troubleshooting Guide

## Purpose

Provide a repeatable diagnostic path that preserves security, limits impact, and identifies the responsible recovery action.

## First response

1. Identify the affected asset, Business Unit, execution context, owner, and correlation or run identifier.
2. Establish customer, data, delivery, and compliance impact.
3. Stop or isolate processing only when continuation threatens security, consent, data integrity, or material customer impact.
4. Preserve safe diagnostics before changing configuration or replaying work.
5. Classify the failure: validation, identity or authorization, data quality, transient dependency, rate limit, platform/runtime, duplicate, or unknown.

## Context paths

| Context | First checks | Recovery focus |
|---|---|---|
| Email | Send audience, identity, data availability, rendering context, content order | Safe fallback, exclusion, send-impact review |
| CloudPages | Request inputs, authorization, response path, page logs | Reject unsafe request; do not reveal diagnostics |
| SQL Query Activity | Source and target schema, target action, activity log, duration | Reduce scope, stage work, validate output |
| Automation Studio | Activity sequence, overlaps, prior stage outputs, run log | Restart only from a safe checkpoint |
| Journey Builder | Entry contract, eligibility, re-entry, split data, path metrics | Correct upstream events and reconcile contacts |
| REST, SOAP, WSProxy | Credentials, scope, account context, response status, pagination | Classify retry safety and reconcile outcomes |

## Rules

- Never retry blindly; confirm idempotency first.
- Never use production customer data in unsecured debugging output.
- Fix the root cause before reactivating repeatedly failing processes.
- Record root cause, corrective action, preventive control, owner, and validation evidence.

## Related Topics

- [Observability and Logging](OBSERVABILITY_AND_LOGGING.md)
- [Release and Go-Live Checklist](RELEASE_AND_GO_LIVE_CHECKLIST.md)
- [Platform facts](../SFMC_PLATFORM_FACTS.md)

## Official References

- [Handle Errors in REST API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/error-handling)

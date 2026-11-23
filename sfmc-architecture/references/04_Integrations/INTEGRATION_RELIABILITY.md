# Integration Reliability

## Purpose

Define the shared reliability contract for REST, SOAP, WSProxy, and external processing.

## Required contract

Every integration defines authentication, authorization, request schema, response schema, timeout, pagination, rate limit, idempotency key, retry policy, correlation identifier, logging, alert owner, reconciliation, and replay procedure.

## Error philosophy

- Classify validation, authorization, transient dependency, permanent dependency, rate-limit, duplicate, and unknown failures.
- Retry only transient failures that are safe to replay; keep retries bounded.
- Never expose secrets or raw upstream errors to end users.
- Preserve safe diagnostics and the recovery decision in structured logs.
- Quarantine invalid records where one record should not fail a batch.
- Reconcile requested, accepted, completed, failed, and duplicate outcomes.

## Related Topics

- [REST API](REST_API.md)
- [SOAP and WSProxy](SOAP_AND_WSPROXY.md)
- [API Integration Solutions](../06_Solution_Patterns/API_INTEGRATION_SOLUTIONS.md)

## Official References

- [Handle Errors in REST API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/error-handling)
- [Script.Util.HttpRequest](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformContentSyndicationScriptUtilHttpRequest.html)

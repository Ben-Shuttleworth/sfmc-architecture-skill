# SOAP and WSProxy

## Purpose

Choose and operate SOAP-backed Salesforce Marketing Cloud Engagement capabilities safely.

## Architecture

Use REST for supported modern APIs. Use WSProxy from non-send SSJS for supported SOAP object operations because it is native to the platform and avoids legacy library translation overhead. Use direct SOAP only when REST and WSProxy cannot meet the requirement.

## Principles

- Confirm object, capability, account feature, Business Unit (BU) context, and API permission before implementation.
- Retrieve in pages and continue until the response indicates completion.
- Batch create or update operations when supported; inspect overall and object-level results.
- Set explicit timeout, retry, idempotency, and reconciliation behavior.
- Do not use WSProxy in send contexts.
- Treat cross-BU context switching as privileged and auditable.

## Related Topics

- [SSJS Reference](../02_Runtimes/SSJS_REFERENCE.md)
- [Integration Reliability](INTEGRATION_RELIABILITY.md)
- [Detailed SSJS reference](../deep_dives/ssjs.md)

## Official References

- [SOAP Web Service Guide](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/web_service_guide)
- [Use SSJS via WSProxy with SOAP API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_useSSJS)

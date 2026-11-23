# API Integration Solutions

## Purpose

Apply a repeatable architecture for reliable Salesforce Marketing Cloud Engagement (SFMC) API integrations.

## Pattern

1. Select REST first; select WSProxy for supported non-send SSJS object operations; use SOAP only for a documented gap.
2. Define Installed Package, scopes, Business Unit context, secret ownership, endpoint, and data contract.
3. Validate requests before issuing side effects.
4. Use bounded timeout, pagination, batch size, and retry behavior.
5. Use an idempotency key or equivalent duplicate-control rule.
6. Capture a correlation identifier and safe structured diagnostics.
7. Reconcile source intent, SFMC acceptance, completion, and downstream outcome.

## Controls

Do not expose credentials, trust upstream data without validation, or retry non-idempotent failures blindly. Move long-running, high-volume, or multi-system orchestration to an appropriate external integration service.

## Related Topics

- [REST API](../04_Integrations/REST_API.md)
- [SOAP and WSProxy](../04_Integrations/SOAP_AND_WSPROXY.md)
- [Integration Reliability](../04_Integrations/INTEGRATION_RELIABILITY.md)

## Official References

- [Server-to-Server Integrations with Client Credentials Grant Type](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/integration-s2s-client-credentials)
- [API Integration Security](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/api-security.html)

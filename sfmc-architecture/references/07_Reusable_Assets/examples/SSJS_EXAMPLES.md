# SSJS Examples

Use these implementation blueprints with the [example asset contract](README.md#asset-contract) and the supported runtime behavior in the [SSJS reference](../../deep_dives/ssjs.md).

## Production CloudPages consent capture

**Context:** Public CloudPages request handling.  
**Outcome:** Use the canonical [production newsletter consent capture](CLOUDPAGES_EXAMPLES.md#production-newsletter-consent-capture) example for the complete GET and POST flow, data contracts, duplicate controls, and safe responses.

Apply supported SSJS runtime behavior from the [SSJS reference](../../deep_dives/ssjs.md) and secure request-handling requirements from [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md). Do not maintain a second implementation of this flow.

## Bounded dependency call

**Context:** Automation Studio Script Activity or CloudPages server-side processing.  
**Outcome:** Use the [server-to-server REST execution](INTEGRATION_EXAMPLES.md#server-to-server-rest-execution) pattern and [Integration Reliability](../../04_Integrations/INTEGRATION_RELIABILITY.md) for response, retry, and reconciliation requirements.

## Related Topics

- [SSJS reference](../../deep_dives/ssjs.md)
- [Integration Reliability](../../04_Integrations/INTEGRATION_RELIABILITY.md)

## Official References

- [Server-Side JavaScript](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_serverSideJavaScript.html)

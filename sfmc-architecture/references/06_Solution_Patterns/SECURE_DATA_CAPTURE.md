# Secure Data Capture

## Purpose

Implement CloudPages data capture as a secure, auditable request-handling flow.

## Pattern

1. Receive only the required request values.
2. Validate type, length, format, allowed values, and request intent.
3. Authenticate and authorize where the action is identity-bound.
4. Sanitize and encode data for its eventual output context.
5. Persist through an idempotent operation with an explicit key.
6. Log a correlation identifier and safe outcome.
7. Return a controlled success or failure response.

## Controls

Encrypted query strings are not authorization. Never trust form or URL input, display raw exceptions, expose secrets, or redirect to unallowlisted destinations. Defer heavy work to Automation Studio or an integration process.

## Related Topics

- [CloudPages Reference](../02_Runtimes/CLOUDPAGES_REFERENCE.md)
- [SSJS Reference](../02_Runtimes/SSJS_REFERENCE.md)
- [Integration Reliability](../04_Integrations/INTEGRATION_RELIABILITY.md)

## Official References

- [GetFormField](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_utilitiesGetFormField.html)
- [API Integration Security](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/api-security.html)

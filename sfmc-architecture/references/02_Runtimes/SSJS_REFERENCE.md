# SSJS Reference

## Platform Constraints

SSJS is server-side SFMC JavaScript, not Node.js or browser JavaScript. Do not assume DOM, filesystem, npm, `fetch()`, or unverified modern ECMAScript behavior.

## Execution Context

Use Core-library SSJS for CloudPages, landing pages, microsites, and applications; use Script Activities for bounded background processing. Use Platform functions or AMPscript for message work.

## Known Limitations

WSProxy is available only in non-send contexts. Script Activities time out after 30 minutes; HTTP calls have their own documented limits.

## Common Mistakes

Do not use Core-library methods in email, create unbounded loops, or use raw SOAP where WSProxy fits.

## Purpose

Guide supported server-side procedural logic in non-message Salesforce Marketing Cloud Engagement (SFMC) contexts.

## Use

Use SSJS for supported procedural work, request handling, controlled data operations, and API calls with validation, timeout handling, logging, and idempotency.

## Security

Validate untrusted inputs, use least-privilege Installed Packages, and keep credentials and sensitive diagnostics out of source, responses, and logs.

## Performance

Keep request processing bounded, use timeouts for HTTP calls, and defer heavy or long-running work to Automation Studio.

## Related Topics

- [Detailed SSJS reference](../deep_dives/ssjs.md)
- [CloudPages Reference](CLOUDPAGES_REFERENCE.md)
- [SOAP and WSProxy](../04_Integrations/SOAP_AND_WSPROXY.md)

## Official References

- [Server-Side JavaScript](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_serverSideJavaScript.html)
- [Platform Functions](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformFunctions)

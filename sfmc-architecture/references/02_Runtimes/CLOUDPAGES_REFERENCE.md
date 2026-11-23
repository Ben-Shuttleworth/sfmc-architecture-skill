# CloudPages Reference

## Purpose

Guide interactive Salesforce Marketing Cloud Engagement (SFMC) page architecture.

## Platform Constraints

CloudPages are public request-response contexts without built-in access control. Implement authentication and authorization where the requested action requires them.

## Architecture

CloudPages can combine browser presentation with AMPscript, SSJS, and REST integrations. Keep client rendering, input validation, authentication, authorization, server processing, persistence, and response handling separate.

## Execution Context

CloudPages are interactive request-response contexts. They are not a replacement for Automation Studio, Journey Builder, or a general web application platform.

## Security

- Treat every query-string, form, and body value as untrusted.
- Encrypted query strings are not authorization.
- Validate, allowlist, sanitize, and output-encode values.
- Use safe user messages and internal structured logging.

## Performance

- Avoid synchronous heavy processing and long dependency chains.

## Related Topics

- [AMPscript Reference](AMPSCRIPT_REFERENCE.md)
- [SSJS Reference](SSJS_REFERENCE.md)
- [Secure Data Capture](../06_Solution_Patterns/SECURE_DATA_CAPTURE.md)
- [Detailed AMPscript reference](../deep_dives/ampscript.md)
- [Detailed SSJS reference](../deep_dives/ssjs.md)

## Official References

- [CloudPages Feature FAQ](https://help.salesforce.com/s/articleView?id=005263976&language=en_US&type=1)
- [Platform Functions](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformFunctions)

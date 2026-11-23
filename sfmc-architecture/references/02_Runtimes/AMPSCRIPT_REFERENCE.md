# AMPscript Reference

## Platform Constraints

AMPscript is proprietary, server-side, function-driven, and context-dependent. It has no user-defined functions, object-oriented model, or external libraries.

## Execution Context

Use in supported email, CloudPages, landing-page, SMS, and mobile contexts. Function behavior varies by context.

## Known Limitations

Do not use AMPscript for bulk transformation, large integrations, or complex reusable application logic. Some functions are send-specific.

## Common Mistakes

Do not write data during preview, assume identity values, or repeat lookups inside loops.

## Purpose

Guide safe, bounded AMPscript use for supported personalization and page-rendering scenarios.

## Use

Use AMPscript for bounded personalization, conditional content, controlled Data Extension lookup, and safe CloudPages link generation. Guard side effects with `_messagecontext`.

## Security

Treat attributes and request values as untrusted. Do not expose sensitive values in rendered output, and do not rely on encrypted query strings as authorization.

## Performance

Keep rendering work bounded. Prepare expensive data upstream and avoid repeated lookups inside loops.

## Related Topics

- [Detailed AMPscript reference](../deep_dives/ampscript.md)
- [AMPscript Syntax Style Guide](AMPSCRIPT_SYNTAX_STYLE_GUIDE.md)
- [Personalization](../06_Solution_Patterns/PERSONALIZATION.md)
- [Email Content and Delivery](../03_Channels_and_Orchestration/EMAIL_CONTENT_AND_DELIVERY.md)

## Official References

- [Getting Started with AMPscript](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-get-started.html)
- [AMPscript Function Reference](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/references)

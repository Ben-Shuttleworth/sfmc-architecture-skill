# Personalization

## Purpose

Deliver relevant content without creating send-time performance, identity, or compliance risk.

## Pattern

1. Define recipient identity and channel eligibility.
2. Prepare expensive attributes, recommendations, and decisions upstream.
3. Render only required values in the message context.
4. Use approved fallbacks for optional data.
5. Guard all side effects against preview and validation contexts.
6. Test with representative and incomplete recipient records.

## Controls

Use AMPscript for simple, bounded personalization. Do not perform large lookups, repeated loops, or external orchestration while rendering. Never expose raw missing-data errors to recipients. Preserve consent and suppression controls outside the personalization layer.

## Related Topics

- [AMPscript Reference](../02_Runtimes/AMPSCRIPT_REFERENCE.md)
- [Email Content and Delivery](../03_Channels_and_Orchestration/EMAIL_CONTENT_AND_DELIVERY.md)
- [Detailed AMPscript reference](../deep_dives/ampscript.md)

## Official References

- [Getting Started with AMPscript](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-get-started.html)
- [Message Context Variable](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-personalization-strings-message-context.html)

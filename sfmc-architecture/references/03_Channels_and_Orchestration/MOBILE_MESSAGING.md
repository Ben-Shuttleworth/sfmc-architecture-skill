# Mobile Messaging

## Purpose

Set architecture rules for MobileConnect and MobilePush activation.

## Principles

- Confirm the specific channel, capability, consent model, identity key, and device or subscriber relationship before design.
- Do not assume that SMS, push, inbox, email, and CloudPages share identical runtime behavior or data availability.
- Maintain channel-specific opt-in, opt-out, suppression, and preference rules.
- Prepare eligibility and personalization data upstream.
- Keep message rendering lightweight and validate mobile-specific functions in the actual target context.
- Record delivery, response, and operational failures using the channel’s supported reporting and logging paths.

## Related Topics

- [Journey Builder](JOURNEY_BUILDER.md)
- [Contact and Subscriber Model](../05_Data_Architecture/CONTACT_AND_SUBSCRIBER_MODEL.md)
- [AMPscript Reference](../02_Runtimes/AMPSCRIPT_REFERENCE.md)

## Official References

- [Use the MobileConnect API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/using-the-mobileconnect-api)
- [Troubleshoot Push Implementation](https://developer.salesforce.com/docs/marketing/mobile-unified-sdk/guide/troubleshoot-push-implementation.html)

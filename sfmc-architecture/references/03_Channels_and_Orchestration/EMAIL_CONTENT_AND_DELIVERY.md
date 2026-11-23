# Email Content and Delivery

## Purpose

Design maintainable, compliant, context-safe email content and delivery processes.

## Architecture

Prepare audience eligibility, consent, suppression, and expensive personalization upstream. At rendering time, use AMPscript or supported native features only for bounded recipient-level content decisions.

## Principles

- Define the sendable Data Extension, identity key, content owner, sender profile, and fallback content.
- Do not assume SubscriberKey equals email address.
- Keep subject, preheader, HTML, and text-body execution order in mind when variables cross components.
- Use the message context variable to avoid preview or validation side effects.
- Fail safely for absent optional attributes; use controlled send errors only for genuine integrity or compliance conditions.
- Test representative recipients, locale, links, unsubscribe behavior, and rendering contexts.

## Related Topics

- [AMPscript Reference](../02_Runtimes/AMPSCRIPT_REFERENCE.md)
- [Personalization](../06_Solution_Patterns/PERSONALIZATION.md)
- [Contact and Subscriber Model](../05_Data_Architecture/CONTACT_AND_SUBSCRIBER_MODEL.md)

## Official References

- [Email Send Definition](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/emailsenddefinition)
- [Message Context Variable](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-personalization-strings-message-context.html)

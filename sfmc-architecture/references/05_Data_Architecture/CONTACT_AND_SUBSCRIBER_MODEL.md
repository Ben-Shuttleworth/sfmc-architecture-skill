# Contact and Subscriber Model

## Purpose

Maintain a clear separation between the enterprise contact model and channel-specific subscriber identity.

## Principles

- Define a stable enterprise person or business identifier and map it deliberately to SubscriberKey and channel identifiers.
- Do not assume a one-to-one relationship between person, email address, mobile number, device, contact, and subscriber.
- Model relationships with explicit cardinality and ownership.
- Preserve consent, suppression, preference, and channel-eligibility data as governed attributes.
- Define contact lifecycle and deletion responsibilities before activation.
- Ensure Journey Entry, sendable Data Extensions, integrations, and personalization use the same identity contract.

## Related Topics

- [Data Extensions](DATA_EXTENSIONS.md)
- [Journey Builder](../03_Channels_and_Orchestration/JOURNEY_BUILDER.md)
- [Email Content and Delivery](../03_Channels_and_Orchestration/EMAIL_CONTENT_AND_DELIVERY.md)

## Official References

- [Create Contacts](https://developer.salesforce.com/docs/marketing/marketing-cloud/references/mc_rest_contacts/createContacts.html)
- [Create a Subscriber](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/creating_a_subscriber.html)

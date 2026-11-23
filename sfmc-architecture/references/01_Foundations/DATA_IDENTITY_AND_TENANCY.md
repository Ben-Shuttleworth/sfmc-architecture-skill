# Data, Identity, and Tenancy

## Purpose

Define the architectural boundaries for customer identity, data ownership, and Business Unit (BU) operation.

## Principles

- Define the business key, SubscriberKey, contact relationship, and channel identifiers independently. Never assume SubscriberKey equals email address.
- Every Data Extension must declare grain, primary keys, nullability, sendability, retention, owner, and permitted consumers.
- Treat the enterprise, each Business Unit, shared Data Extensions, Installed Packages, and permissions as explicit trust boundaries.
- Do not hardcode MIDs, Business Unit IDs, folder IDs, or package identifiers.
- Configure and document which BU owns creation, maintenance, activation, and deletion of every asset.
- Use shared data only where its access model and purpose are deliberate.

## Architecture

The contact model is the logical view of a person; Data Extensions are physical storage. Relationships must be cardinality-aware and must not create unintended one-to-many activation records.

## Related Topics

- [Data Extensions](../05_Data_Architecture/DATA_EXTENSIONS.md)
- [Contact and Subscriber Model](../05_Data_Architecture/CONTACT_AND_SUBSCRIBER_MODEL.md)
- [Platform facts](../SFMC_PLATFORM_FACTS.md)

## Official References

- [Manage Data Extensions](https://help.salesforce.com/s/articleView?id=mktg.mc_cab_create_a_new_data_extension.htm&language=en_US&type=5)
- [Data Designer in Contact Builder](https://help.salesforce.com/s/articleView?id=sf.mc_cab_data_designer.htm&language=en_US&type=5)

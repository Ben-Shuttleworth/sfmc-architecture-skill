# Data Extensions

## Purpose

Define physical data-storage standards for Salesforce Marketing Cloud Engagement Data Extensions.

## Required design record

Every Data Extension documents its name, external key, owner, Business Unit, purpose, grain, primary keys, field definitions, nullability, sendability, retention, source systems, permitted consumers, and change owner.

## Principles

- Use the Data Extension name in Query Activities and the external key for APIs and supported scripting contexts.
- Design keys for the actual record grain; do not use email address by default.
- Define retention before loading personal or operational data.
- Keep shared Data Extensions deliberate and documented.
- Separate raw, staging, curated, activation, error, and log data by purpose.
- Never change schema, key, or field behavior without impact assessment and dependent-asset validation.

## Related Topics

- [Data, Identity, and Tenancy](../01_Foundations/DATA_IDENTITY_AND_TENANCY.md)
- [Contact and Subscriber Model](CONTACT_AND_SUBSCRIBER_MODEL.md)
- [SQL Design Patterns](SQL_DESIGN_PATTERNS.md)

## Official References

- [Manage Data Extensions](https://help.salesforce.com/s/articleView?id=mktg.mc_cab_create_a_new_data_extension.htm&language=en_US&type=5)

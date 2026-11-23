# Platform Landscape

## Purpose

Map the independent Salesforce Marketing Cloud Engagement (SFMC) subsystems and their architectural responsibilities.

## Architecture

SFMC is a collection of specialized services, not one uniform runtime:

- **Content and Email:** message composition, personalization, and delivery.
- **CloudPages:** interactive web experiences.
- **Automation Studio:** scheduled and background processing.
- **Journey Builder:** event-driven customer orchestration.
- **Contact Builder and Data Extensions:** customer data and relationships.
- **MobileConnect and MobilePush:** channel-specific mobile activation.
- **REST, SOAP, and WSProxy:** programmatic integration and object operations.

## Principles

- Assign each responsibility to the subsystem designed for it.
- Make transitions between subsystems explicit through data contracts and operational ownership.
- Do not assume a feature, package, permission, or asset is available in every Business Unit.
- Use native configuration before code where it meets the need.

## Related Topics

- [Execution Context Catalog](EXECUTION_CONTEXT_CATALOG.md)
- [Data, Identity, and Tenancy](DATA_IDENTITY_AND_TENANCY.md)
- [Platform facts](../SFMC_PLATFORM_FACTS.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

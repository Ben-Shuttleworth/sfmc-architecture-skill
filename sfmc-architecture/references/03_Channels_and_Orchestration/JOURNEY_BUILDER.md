# Journey Builder

## Purpose

Design event-driven customer orchestration with reliable entry, decisioning, and exit behavior.

## Architecture

Journey Builder coordinates customer lifecycle steps; it should not contain heavy data transformation or depend on unclear entry data. Prepare and validate entry data upstream, then make each event contract explicit.

## Principles

- Define entry source, identity, eligibility, consent, re-entry rules, and data freshness.
- Distinguish Journey Entry from Journey Execution and from contact-data updates.
- Keep decision splits readable and based on governed fields.
- Make wait, exit, and error behavior intentional.
- Use automation or integration stages for enrichment, reconciliation, and large processing.
- Monitor entry volume, exclusion reasons, path distribution, and downstream failures.

## Related Topics

- [Event-Driven Journeys](../06_Solution_Patterns/EVENT_DRIVEN_JOURNEYS.md)
- [Contact and Subscriber Model](../05_Data_Architecture/CONTACT_AND_SUBSCRIBER_MODEL.md)
- [Execution Context Catalog](../01_Foundations/EXECUTION_CONTEXT_CATALOG.md)

## Official References

- [Get Started with the Journey Builder API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/get-started-jb.html)
- [Journey Builder Glossary](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/glossary.html)

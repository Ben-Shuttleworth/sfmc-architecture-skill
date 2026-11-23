# Event-Driven Journeys

## Purpose

Create Journey Builder solutions that process only valid, eligible customer events.

## Pattern

1. Define event producer, schema, identity key, eligibility, consent, and freshness rules.
2. Validate and enrich the event before Journey Builder entry.
3. Deduplicate or make re-entry behavior explicit.
4. Enter the journey with the minimum governed data required for decisions.
5. Use activities and splits for customer orchestration, not bulk processing.
6. Record entry, exclusion, exit, and downstream outcome metrics.
7. Reconcile events that were accepted, delayed, rejected, or duplicated.

## Controls

Do not rely on unclear contact state, rapidly changing source data, or implicit re-entry behavior. Keep expensive enrichment, large updates, and recovery processes upstream in automation or integration layers.

## Related Topics

- [Journey Builder](../03_Channels_and_Orchestration/JOURNEY_BUILDER.md)
- [Contact and Subscriber Model](../05_Data_Architecture/CONTACT_AND_SUBSCRIBER_MODEL.md)
- [Scheduled Data Pipelines](SCHEDULED_DATA_PIPELINES.md)

## Official References

- [Fire an Entry Event](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/how-to-fire-an-event)
- [Event Definition Overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/event-definition-key.html)

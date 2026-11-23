# Automation and Journey Examples

Use these architecture blueprints with the [example asset contract](README.md#asset-contract), [Automation Studio](../../03_Channels_and_Orchestration/AUTOMATION_STUDIO.md), and [Journey Builder](../../03_Channels_and_Orchestration/JOURNEY_BUILDER.md).

## Scheduled audience publication

**Context:** Automation Studio.  
**Business problem:** Publish a daily eligible audience without exposing a partially processed Data Extension.

| Stage | Input | Output | Control |
|---|---|---|---|
| Ingest | Source extract | Stage_RawCustomer | Schema and freshness check |
| Validate | Stage_RawCustomer | Error_CustomerValidation | Invalid records quarantined |
| Transform | Valid rows | Stage_EligibleAudience | SQL with explicit key and consent rules |
| Publish | Stage_EligibleAudience | Audience_Product_Offer | Row-count and uniqueness validation |
| Observe | Run metrics | Log_AudiencePublication | Alert and replay owner |

Do not overwrite the activation target until its validation gate passes. Each stage must be restartable and own an idempotency rule.

## Event-driven welcome journey

**Context:** Upstream event producer plus Journey Builder.  
**Business problem:** Welcome a newly consented subscriber only once per approved lifecycle event.

1. Producer validates identity, consent, event version, and source timestamp.
2. Automation or integration stage deduplicates the event and records a correlation ID.
3. Journey Entry receives the governed event contract.
4. Journey sends content using prepared personalization data.
5. Exit and exclusion metrics reconcile accepted, rejected, and completed events.

Do not perform bulk enrichment or high-volume reconciliation inside Journey Builder.

## Related Topics

- [Automation Studio](../../03_Channels_and_Orchestration/AUTOMATION_STUDIO.md)
- [Journey Builder](../../03_Channels_and_Orchestration/JOURNEY_BUILDER.md)
- [Event-Driven Journeys](../../06_Solution_Patterns/EVENT_DRIVEN_JOURNEYS.md)

## Official References

- [Retrieving and Segmenting Data with a SQL Query Activity](https://help.salesforce.com/s/articleView?id=sf.mc_as_using_the_query_activity.htm&language=en_US&type=5)
- [Fire an Entry Event](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/how-to-fire-an-event)

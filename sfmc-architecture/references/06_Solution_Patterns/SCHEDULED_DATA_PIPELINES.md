# Scheduled Data Pipelines

## Purpose

Operate scheduled data processing as a recoverable Salesforce Marketing Cloud Engagement pipeline.

## Pattern

1. Ingest data to a controlled raw or staging Data Extension.
2. Validate schema, key, volume, and freshness.
3. Transform with bounded SQL Query Activities.
4. Quarantine invalid or conflicting records.
5. Publish validated output to the activation target.
6. Record run metrics and reconcile expected versus actual results.
7. Alert the owner and provide a replay path for failure.

## Controls

Sequence conflicting activities, use idempotent target behavior, and do not overwrite production activation data until validation passes. Split large transformations into intermediate stages and define a recovery owner for each stage.

## Related Topics

- [Automation Studio](../03_Channels_and_Orchestration/AUTOMATION_STUDIO.md)
- [SQL Design Patterns](../05_Data_Architecture/SQL_DESIGN_PATTERNS.md)
- [Integration Reliability](../04_Integrations/INTEGRATION_RELIABILITY.md)

## Official References

- [Automation Studio Support](https://help.salesforce.com/s/articleView?id=mktg.mc_co_automation_studio_support.htm&language=en_US&type=5)

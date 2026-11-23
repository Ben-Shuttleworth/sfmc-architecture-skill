# SQL Design Patterns

## Purpose

Apply compatible, observable SQL Query Activity patterns to Salesforce Marketing Cloud Engagement data workloads.

## Patterns

- **Stage–Validate–Transform–Publish:** use intermediate Data Extensions to make large transformations restartable.
- **Eligibility audience:** join governed profiles to consent and suppression inputs, then publish only eligible records.
- **Deduplication:** define the retained record and field-level survivorship before producing a unique-key target.
- **Incremental processing:** use a documented watermark and overlap strategy, then reconcile late-arriving data.
- **Quarantine:** separate invalid, incomplete, or conflicting records from valid output.
- **Operational metrics:** produce run counts, duplicate counts, and exception counts alongside activation data.

## Rules

Use explicit columns, bounded dates, documented identity joins, and target actions that match the recovery strategy. Do not use unsupported SQL features or conceal business rules in unexplained query expressions.

## Related Topics

- [SQL Query Activities Reference](../02_Runtimes/SQL_QUERY_ACTIVITIES_REFERENCE.md)
- [Scheduled Data Pipelines](../06_Solution_Patterns/SCHEDULED_DATA_PIPELINES.md)
- [Detailed SQL reference](../deep_dives/sql.md)

## Official References

- [SQL Reference](https://help.salesforce.com/s/articleView?id=mktg.mc_as_sql_reference.htm&language=en_US)

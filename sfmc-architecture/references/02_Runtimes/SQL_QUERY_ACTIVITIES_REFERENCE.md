# SQL Query Activities Reference

## Platform Constraints

Query Activities use a constrained SQL dialect, not full SQL Server. They run supported `SELECT` queries against Data Extensions and data views and write through configured target behavior.

## Execution Context

Use in Automation Studio for scheduled or triggered background data transformation.

## Known Limitations

Do not assume support for temporary tables, CTEs, variables, transactions, dynamic SQL, DDL, stored procedures, or `MERGE`. Queries time out after 30 minutes.

## Common Mistakes

Do not use `SELECT *` with joins, source external keys instead of Data Extension names, unbounded data-view queries, or target Data Extensions as sources.

## Purpose

Guide safe, set-based Query Activity design for background data transformation and audience preparation.

## Use

Use Query Activities for segmentation, staging, aggregation, data-quality checks, and audience preparation.

## Security

Apply consent, suppression, channel eligibility, and Business Unit access rules before producing an activation audience. Do not expose sensitive data through unnecessary target fields or operational outputs.

## Performance

Filter early, select explicit fields, stage complex transformations, and validate row counts before activation. Redesign queries that approach the platform timeout.

## Related Topics

- [Detailed SQL reference](../deep_dives/sql.md)
- [Data Extensions](../05_Data_Architecture/DATA_EXTENSIONS.md)
- [SQL Design Patterns](../05_Data_Architecture/SQL_DESIGN_PATTERNS.md)

## Official References

- [SQL Reference](https://help.salesforce.com/s/articleView?id=mktg.mc_as_sql_reference.htm&language=en_US)
- [Build a SQL Query Activity](https://help.salesforce.com/s/articleView?id=sf.mc_as_use_the_sql_query_activity.htm&language=en_US&type=5)

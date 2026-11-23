# SQL Query Activities

## Platform Constraints

SQL Query Activities use a constrained SQL dialect based on, but not identical to, SQL Server 2016. They support `SELECT` queries against Data Extensions and supported data views; they are not a full SQL Server environment.

Query Activities run with `READ UNCOMMITTED` isolation. Do not rely on transactions, locking, or perfectly consistent reads while other processes modify the same data. [Platform facts](../SFMC_PLATFORM_FACTS.md) take precedence over this reference.

## Execution Context

Use SQL Query Activities in Automation Studio for scheduled, triggered, or manually run background data processing. They read from Data Extensions and supported data views and write results to a separately configured target Data Extension.

Use a Query Activity when the problem is set-based: segmentation, deduplication, staging, audience preparation, aggregate calculation, data quality checks, or operational reporting. Do not use it for interactive CloudPages requests, message-time personalization, procedural orchestration, or arbitrary external integration.

## Known Limitations

- Only `SELECT` statements are supported for Query Activities; the target write behavior is configured outside the SQL text.
- Queries can access Data Extensions and supported data views, subject to Business Unit (BU) and enterprise context.
- Use the Data Extension **name**, not its external key, in query text.
- The target Data Extension cannot also be a source Data Extension in the query.
- Query Activities time out after 30 minutes; recurring runs approaching 10 minutes require redesign.
- Unsupported constructs include variables, cursors, user-defined functions, transactions and locking, `GOTO`, `PRINT`, `sp_*` stored procedures, `EXEC`, temporary tables, common table expressions, and open-ended `--` comments.
- Do not assume support for DDL, dynamic SQL, table variables, query hints, or `MERGE`.
- A query that combines `JOIN` with `SELECT *` is not permitted; enumerate the required fields.
- Data-view availability, retention, time zone, and enterprise behavior vary. Verify the exact data-view contract before use.

## Common Mistakes

- Treating Query Activities as full T-SQL.
- Using a source Data Extension’s external key instead of its name.
- Selecting `*`, especially with joins.
- Applying functions to filtered or joined source columns, causing non-SARGable predicates and full scans.
- Querying all historical data when a bounded date range is sufficient.
- Reusing the target Data Extension as a source.
- Choosing an unsafe target action, especially `Overwrite`, without recovery controls.
- Joining on email address instead of the documented identity key.
- Running conflicting imports, queries, or updates against the same Data Extension concurrently.
- Assuming an activity failure automatically explains which records were invalid.

## Purpose

Use SQL Query Activities to prepare structured, governed data for sends, journeys, automations, reporting, and downstream processes. The primary output is a target Data Extension with a documented schema, ownership, retention policy, and write behavior.

Use AMPscript for simple send-time personalization, SSJS for bounded procedural logic, and REST or external processing for large or long-running integration workloads. SQL Query Activities are the preferred native Salesforce Marketing Cloud Engagement choice for bulk data transformation that can be expressed as a supported `SELECT` statement.

## Architecture

Use a staged data-pipeline pattern:

1. Define source contracts: grain, key, freshness, ownership, and consent status.
2. Filter early to reduce the processing set.
3. Join on documented keys with explicit cardinality expectations.
4. Write to a staging or final target using the deliberate target action.
5. Validate row counts, key uniqueness, null rates, and eligibility outcomes.
6. Publish only validated outputs to sendable, journey-entry, or downstream Data Extensions.
7. Preserve run diagnostics and quarantine exceptions where individual records must be investigated.

For complex transformations, split the workflow into smaller Query Activities with intermediate Data Extensions. This improves performance, makes failures diagnosable, and creates restart points.

## Best Practices

- State the business purpose, source and target schema, expected row count, and target action for every query.
- Use explicit field lists and meaningful aliases.
- Filter early and constrain data-view queries to the smallest valid date window.
- Join on indexed or primary-key fields where possible and preserve the intended grain.
- Use `INNER JOIN` only when exclusion of unmatched records is intended; use `LEFT JOIN` where unmatched records require a controlled decision.
- Select only the fields required by the target contract.
- Stage large joins, aggregates, and deduplication rather than producing one monolithic query.
- Use `CASE`, `CAST`, `CONVERT`, joins, unions, nested queries, and grouping only within documented support and with representative-volume testing.
- Validate syntax, inspect activity logs, and test against realistic data before scheduling.
- Stagger automations and prevent overlapping processes that modify or query the same high-volume Data Extensions.
- Use `ENT.` only when enterprise/shared Data Extension access is intentional and documented.
- Re-save a Query Activity after a source or target schema change so its validated definition is refreshed.

## Anti-patterns

| Anti-pattern | Why it fails | Preferred approach |
|---|---|---|
| One large all-history query | Increases scan volume and timeout risk. | Restrict dates and stage processing. |
| `SELECT *` with a join | Not permitted and obscures the target contract. | Select each required field explicitly. |
| Function on a `WHERE` or `JOIN` source column | Can prevent effective index use. | Filter using the stored value or pre-stage a normalized field. |
| Direct overwrite of a production audience | A failed or incomplete run can remove usable output. | Stage, validate, then publish with controlled ownership. |
| Querying and importing the same DE concurrently | Creates contention and inconsistent reads. | Sequence activities and establish data ownership. |
| Email-based identity join | Email can change and is not a universal identifier. | Join on the documented SubscriberKey or business identifier. |
| Data-quality correction hidden in a query | Makes rejected or altered records hard to audit. | Stage, classify, and quarantine exceptions. |

## Performance

- Query Activities have a 30-minute timeout. Redesign a query that consistently approaches 10 minutes.
- Filter early, use narrow date windows, and avoid processing columns not needed by the target.
- Avoid `SELECT *`, especially with joins.
- Keep functions off the left side of predicates used for filtering or joining when possible.
- Join on appropriately designed keys and avoid accidental one-to-many row multiplication.
- Split high-volume work into intermediate Data Extensions with measurable stages.
- Avoid concurrent processes that contend for the same Data Extensions.
- Schedule high-volume automations away from congested execution windows.
- Measure input rows, output rows, duplicates, rejected rows, duration, and repeat failures.

## Security

- Apply consent, suppression, channel eligibility, and Business Unit access rules before producing an activation audience.
- Select the minimum personal data required by the target purpose.
- Do not use sensitive values as a substitute for a stable identity key.
- Protect Data Extension access through least-privilege roles and explicit ownership.
- Treat shared and enterprise Data Extensions as cross-BU trust boundaries.
- Do not expose raw personal data in operational error reports when counts, keys, or masked values are sufficient.
- Use retention settings appropriate to the target’s business and regulatory purpose.
- Review overwrite targets carefully; target actions can materially alter downstream activation data.

## Debugging

1. Confirm the Query Activity context, source/target Business Unit, and `ENT.` usage.
2. Verify every source Data Extension name, field name, data type, and target-field mapping.
3. Validate syntax in Automation Studio before scheduling.
4. Start with a bounded date range and a reduced source set.
5. Inspect each join for unintended duplicate multiplication or lost rows.
6. Compare source, intermediate, and target row counts against expected cardinality.
7. Check the activity log for syntax, timeout, schema, primary-key, and target-write errors.
8. If a schema changed, re-save the Query Activity to refresh its validated definition.
9. For recurrent timeouts, isolate the most expensive stage, reduce scope, and use intermediate targets.
10. If an automation or Query Activity is suspended after repeated failures, correct the root cause before reactivation.

## Examples

### Prepare an eligible email audience

**Context:** Automation Studio Query Activity.  
**Sources:** `CustomerProfile`, `EmailSuppression`.  
**Target:** `Audience_Product_Offer`, configured with an appropriate primary key and retention policy.  
**Behavior:** Produces one eligible audience row per profile record that is not suppressed.

```sql
SELECT
    profile.SubscriberKey,
    profile.EmailAddress,
    profile.LocaleCode,
    profile.PreferredCategory
FROM [CustomerProfile] AS profile
LEFT JOIN [EmailSuppression] AS suppression
    ON profile.SubscriberKey = suppression.SubscriberKey
WHERE profile.IsEmailEligible = 1
  AND suppression.SubscriberKey IS NULL
```

Before production use, confirm that `CustomerProfile` has one row per `SubscriberKey`, that suppression data is current, and that the target action cannot accidentally retain stale audience members.

### Create a staged recent-engagement audience

**Context:** Automation Studio Query Activity.  
**Source:** The applicable tracking data view and a governed customer Data Extension.  
**Target:** `Stage_RecentEmailEngagement`.  
**Behavior:** Limits the workload to a defined recent period before later audience processing.

```sql
SELECT DISTINCT
    sent.SubscriberKey,
    sent.EventDate AS SentDate
FROM _Sent AS sent
WHERE sent.EventDate >= DATEADD(day, -30, GETDATE())
```

Confirm data-view retention, time-zone behavior, and the target’s intended grain before relying on this output. Add the required job, list, or business filters for the campaign’s actual scope.

### Explicit deduplication stage

**Context:** Automation Studio Query Activity.  
**Source:** `Stage_ImportedCustomer`.  
**Target:** `Stage_CustomerKeyCandidates`.  
**Behavior:** Produces distinct identity candidates for a later, separately owned validation stage.

```sql
SELECT
    source.SubscriberKey
FROM [Stage_ImportedCustomer] AS source
WHERE source.SubscriberKey IS NOT NULL
GROUP BY source.SubscriberKey
```

This does not determine which of several conflicting source records is authoritative. Resolve field-level survivorship in a separately documented stage.

## Related Topics

- [Platform facts](../SFMC_PLATFORM_FACTS.md)
- [Architecture skill](../../SKILL.md)
- [Execution Context Catalog](../01_Foundations/EXECUTION_CONTEXT_CATALOG.md)
- [Security, Performance, and Operations](../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)
- [Automation Studio](../03_Channels_and_Orchestration/AUTOMATION_STUDIO.md)
- [Data Extensions](../05_Data_Architecture/DATA_EXTENSIONS.md)
- [SQL Design Patterns](../05_Data_Architecture/SQL_DESIGN_PATTERNS.md)
- [Scheduled Data Pipelines](../06_Solution_Patterns/SCHEDULED_DATA_PIPELINES.md)
- [SQL Query Examples](../07_Reusable_Assets/examples/SQL_QUERY_EXAMPLES.md)
- [SQL Snippets](../07_Reusable_Assets/snippets/SQL_SNIPPETS.md)

## Official References

- [SQL Reference](https://help.salesforce.com/s/articleView?id=mktg.mc_as_sql_reference.htm&language=en_US)
- [Retrieving and Segmenting Data with a SQL Query Activity](https://help.salesforce.com/s/articleView?id=sf.mc_as_using_the_query_activity.htm&language=en_US&type=5)
- [Build a SQL Query Activity](https://help.salesforce.com/s/articleView?id=sf.mc_as_use_the_sql_query_activity.htm&language=en_US&type=5)
- [Data Views](https://help.salesforce.com/s/articleView?id=mktg.mc_as_data_views.htm&language=en_US&type=5)
- [Query Activity Timeout Error](https://help.salesforce.com/s/articleView?id=000392804&language=en_US&type=1)
- [Automation Studio and SQL in Marketing Cloud](https://developer.salesforce.com/blogs/2023/02/automation-studio-and-sql-in-marketing-cloud)

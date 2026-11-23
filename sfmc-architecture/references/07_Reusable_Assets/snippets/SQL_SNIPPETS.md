# SQL Snippets

Use these fragments with the [required use checks](README.md#required-use-checks) and [SQL reference](../../deep_dives/sql.md). Unless stated otherwise, they omit complete data contracts, target configuration, error handling, run metrics, reconciliation, and release controls.

## Date-bounded source filter

**Context:** Automation Studio Query Activity.

    WHERE source.UpdatedDateUTC >= DATEADD(day, -7, GETDATE())

Confirm time-zone semantics and window sizing through the [SQL reference](../../deep_dives/sql.md).

## Exclude suppressed records

**Context:** Query Activity with documented SubscriberKey identity.

    LEFT JOIN [EmailSuppression] AS suppression
        ON profile.SubscriberKey = suppression.SubscriberKey
    WHERE suppression.SubscriberKey IS NULL

Apply eligibility and consent conditions through the owning audience design.

## Explicit uniqueness stage

**Context:** Query Activity.

    SELECT
        source.SubscriberKey
    FROM [Stage_ImportedCustomer] AS source
    WHERE source.SubscriberKey IS NOT NULL
    GROUP BY source.SubscriberKey

Use [SQL Design Patterns](../../05_Data_Architecture/SQL_DESIGN_PATTERNS.md) for survivorship and staged transformation.

## Related Topics

- [SQL reference](../../deep_dives/sql.md)
- [SQL Design Patterns](../../05_Data_Architecture/SQL_DESIGN_PATTERNS.md)

## Official References

- [SQL Reference](https://help.salesforce.com/s/articleView?id=mktg.mc_as_sql_reference.htm&language=en_US)

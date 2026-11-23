# SQL Query Examples

Use these implementation blueprints with the [example asset contract](README.md#asset-contract) and the constraints and controls in the [SQL reference](../../deep_dives/sql.md).

## Eligible email audience

**Context:** Automation Studio Query Activity.  
**Sources:** CustomerProfile and EmailSuppression.  
**Target:** Audience_Product_Offer, with a documented primary key and retention policy.  
**Outcome:** Produces only current, email-eligible, non-suppressed customer records.

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

Confirm that the profile source has one row per SubscriberKey and that the target action does not retain stale audience members.

For reusable date-bound, suppression, and identity-stage fragments, use the [SQL Snippets](../snippets/SQL_SNIPPETS.md). Apply the documented Query Activity constraints, data-view behavior, and target controls in the [SQL reference](../../deep_dives/sql.md).

## Related Topics

- [SQL reference](../../deep_dives/sql.md)
- [SQL Design Patterns](../../05_Data_Architecture/SQL_DESIGN_PATTERNS.md)

## Official References

- [SQL Reference](https://help.salesforce.com/s/articleView?id=mktg.mc_as_sql_reference.htm&language=en_US)

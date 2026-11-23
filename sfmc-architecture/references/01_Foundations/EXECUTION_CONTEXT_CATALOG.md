# Execution Context Catalog

## Purpose

Provide the mandatory first decision for every Salesforce Marketing Cloud Engagement (SFMC) solution.

## Contexts

| Context | Intended use | Primary choices |
|---|---|---|
| Email or message rendering | Per-recipient content | AMPscript; supported Platform functions |
| CloudPages or landing page | Interactive browser request | HTML/CSS/client JavaScript, AMPscript, SSJS, REST |
| Automation Studio | Scheduled background work | SQL Query Activity, Script Activity, imports, extracts |
| Journey Builder | Event-driven lifecycle | Entry events, activities, decisions, updates |
| REST integration | External system interaction | OAuth-protected REST calls |
| SOAP integration | Supported legacy or email-object operations | SOAP; WSProxy from non-send SSJS |
| Mobile messaging | Channel-specific activation | Native mobile capabilities and verified personalization |

## Decision rule

If the context is unknown, stop and clarify. Do not select a technology, provide code, or imply feature support until it is known.

## Related Topics

- [Technology Selection Guide](TECHNOLOGY_SELECTION_GUIDE.md)
- [Platform facts](../SFMC_PLATFORM_FACTS.md)
- [Architecture skill](../../SKILL.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

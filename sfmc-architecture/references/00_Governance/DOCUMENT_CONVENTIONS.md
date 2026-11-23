# Document Conventions

## Purpose

Establish the required structure and cross-reference behavior for this Salesforce Marketing Cloud Engagement (SFMC) library.

## Required metadata

Every document is governed by the default audience, scope, prerequisites, execution context, platform constraints, security implications, and performance implications in the [library metadata](../../SKILL.md#scope-and-authority). A document states any narrower or different applicability next to the relevant guidance or example.

Every document states its purpose, related topics, and official references. Technical guidance also states its applicable execution context, platform constraints, security implications, and performance implications. These may be explicit sections or, for reusable assets, fields next to each example or snippet. Link to the authoritative document instead of repeating inherited guidance solely to create a heading.

Language documents begin in this order: **Platform Constraints**, **Execution Context**, **Known Limitations**, and **Common Mistakes**. Detailed language documents in `references/` also cover architecture, best practices, anti-patterns, performance, security, debugging, examples, related topics, and official references. Runtime summaries in `02_Runtimes/` link to the applicable detailed reference or solution pattern instead of duplicating that material.

## Content rules

- Use `MUST`, `MUST NOT`, `SHOULD`, and `MAY` consistently.
- Link to the authoritative document instead of duplicating its rules.
- Separate verified fact, architecture recommendation, and uncertainty.
- State the precise execution context before presenting code.
- Examples must describe inputs, outputs, dependencies, data assumptions, errors, security, and scale. Shared requirements may be inherited through an explicit link to the reusable-example asset contract; each example still states its execution context, business outcome, and any narrower or different controls.
- Snippets must state their safe context and the production controls they omit. A snippet file may state controls shared by every contained fragment once, provided each fragment states its context and any exceptions.
- Use official Salesforce references for platform claims.

## Terminology

Use consistent terms: Business Unit, MID, SubscriberKey, Contact, Data Extension, Journey Entry, Journey Execution, Query Activity, and Installed Package. Never use “SFMC JavaScript” to imply Node.js or browser behavior.

## Related Topics

- [Platform facts](../SFMC_PLATFORM_FACTS.md)
- [Architecture skill](../../SKILL.md)
- [Research and Citation Policy](RESEARCH_AND_CITATION_POLICY.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

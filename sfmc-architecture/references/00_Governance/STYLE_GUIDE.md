# Style Guide

## Purpose

Define consistent formatting, terminology, naming, and documentation quality standards for the Salesforce Marketing Cloud Engagement (SFMC) Skill Library.

## Scope

This guide applies to every reference, foundation, solution pattern, example, snippet, and operations document. It works with [DOCUMENT_CONVENTIONS.md](DOCUMENT_CONVENTIONS.md) and defers to [SFMC_PLATFORM_FACTS.md](../SFMC_PLATFORM_FACTS.md) for platform truth.

## Audience

Authors, reviewers, and AI contributors maintaining the SFMC Skill Library.

## Prerequisites

- [SFMC_PLATFORM_FACTS.md](../SFMC_PLATFORM_FACTS.md)
- [DOCUMENT_CONVENTIONS.md](DOCUMENT_CONVENTIONS.md)
- [RESEARCH_AND_CITATION_POLICY.md](RESEARCH_AND_CITATION_POLICY.md)

## Execution Context

This is a governance document applied across all SFMC execution contexts. The document being authored must identify its own applicable context.

## Platform Constraints

Formatting and terminology must not imply capabilities that conflict with [SFMC_PLATFORM_FACTS.md](../SFMC_PLATFORM_FACTS.md).

## Security and Performance

Documentation must state context-appropriate security controls, performance assumptions, bounded work, error behavior, and operational ownership wherever implementation guidance is provided.

## Writing standards

- Lead with the outcome, then explain the architecture and reasoning.
- Use plain technical language and avoid unnecessary jargon.
- Use MUST, MUST NOT, SHOULD, and MAY consistently.
- Separate verified platform facts, architecture recommendations, examples, and assumptions.
- Never present an inferred or unverified capability as fact.
- Prefer one authoritative definition and cross-reference it elsewhere.
- Use present tense and active voice for requirements and procedures.

## Structure and formatting

- Use one H1 title per document.
- Use H2 headings for major sections and H3 headings for subsections.
- Leave a blank line between headings, paragraphs, lists, and tables.
- Use tables for comparisons and field mappings; use lists for requirements and checks.
- Use fenced code blocks with a language identifier for executable examples.
- State execution context, inputs, outputs, dependencies, security, performance, and error behavior next to every reusable example.
- Use relative Markdown links for internal documents and direct HTTPS links for official external references.
- Use descriptive link labels rather than raw URLs.

## SFMC terminology

Use these canonical forms:

- Salesforce Marketing Cloud Engagement on first mention; SFMC thereafter.
- Business Unit (BU); use BU only after defining the abbreviation in the current document.
- MID, SubscriberKey, Contact, Data Extension, Query Activity, Journey Entry, Journey Execution, Installed Package, CloudPages, AMPscript, SSJS, WSProxy, REST API, and SOAP API.
- Use CloudPage for one page and CloudPages for the product or page family.
- Describe SSJS as server-side SFMC JavaScript, never as Node.js or browser JavaScript.
- Describe SQL Query Activities as constrained SQL, never as full T-SQL.

## Naming

- Use descriptive, stable names that communicate domain and purpose.
- Use the existing library’s uppercase file and directory naming conventions.
- Use lowerCamelCase for SSJS identifiers, camelCase for AMPscript variables with an at-sign, and PascalCase for AMPscript function names.
- Use PascalCase for Data Extension fields and explicit UTC suffixes for UTC timestamps.
- Never embed secrets, personal data, MIDs, folder IDs, or environment-specific identifiers in reusable names or code.

## Code and examples

- Identify language, runtime, and execution context before code.
- Use production-oriented examples with validation, safe errors, bounded work, and comments explaining platform-specific intent.
- Do not use syntax-only demonstrations as production guidance.
- Mark configuration values as deployment configuration and never provide fake credentials.
- Keep examples small, but state omitted controls and required production review.

## References and maintenance

- Every platform claim links to an official Salesforce source or the canonical platform-facts document.
- Every document links to its prerequisites and related downstream patterns where applicable.
- Review links and time-sensitive claims whenever a document changes.
- Preserve the source-of-truth hierarchy: platform facts, architecture guidance, governance, references, patterns, then reusable assets.

## Related Topics

- [Document Conventions](DOCUMENT_CONVENTIONS.md)
- [Research and Citation Policy](RESEARCH_AND_CITATION_POLICY.md)
- [Architecture skill](../../SKILL.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

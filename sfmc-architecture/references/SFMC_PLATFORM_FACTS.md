# Salesforce Marketing Cloud Engagement Platform Facts

> **Canonical Platform Knowledge**
>
> This document defines the authoritative facts, constraints, assumptions, and engineering principles that every AI agent contributing to the Salesforce Marketing Cloud Skill Library must follow.
>
> Every generated document, example, code sample, architecture recommendation, review, and decision guide shall conform to this document.
>
> If any generated content conflicts with this document, this document takes precedence.

---

## Purpose

Salesforce Marketing Cloud Engagement (SFMC) is **not** a general software development platform.

It consists of multiple proprietary runtimes, execution environments, APIs, and development models, each with its own capabilities and limitations.

Many AI models incorrectly assume that SFMC behaves like modern web development environments such as:

* Node.js
* Modern browsers
* Microsoft SQL Server
* .NET
* Java
* PHP

These assumptions frequently produce incorrect code.

The purpose of this document is to prevent those errors.

---

## Core Principles

Always generate solutions that are:

* Production ready
* Secure
* Performant
* Maintainable
* Scalable
* Compatible with SFMC
* Appropriate for the execution context

Never generate code that merely demonstrates language syntax.

Every solution should solve a real business problem.

---

## Source of Truth

When uncertainty exists:

1. Salesforce Official Documentation
2. Salesforce Release Notes
3. Salesforce Developer Documentation
4. Verified platform behaviour
5. Community consensus

Never invent unsupported features.

Never guess platform capabilities.

When uncertainty exists:

Explicitly state the uncertainty.

---

## SFMC Architecture

SFMC consists of multiple independent subsystems.

Examples include:

* Email Studio
* Journey Builder
* Contact Builder
* Content Builder
* Automation Studio
* MobileConnect
* MobilePush
* CloudPages
* Intelligence Reports
* REST APIs
* SOAP APIs

Do not assume all features are available everywhere.

---

## Execution Contexts

Always identify the execution context before generating code.

Possible contexts include:

* Email
* CloudPage
* Landing Page
* Automation Studio Script Activity
* Journey Builder
* Transactional Messaging
* MobileConnect
* MobilePush
* REST Integration
* SOAP Integration

If the execution context is unknown:

Ask for clarification.

Never assume.

---

## Technology Selection

Prefer the simplest technology capable of solving the problem.

General guidance:

Simple personalization

→ AMPscript

Complex business logic

→ SSJS

Data transformations

→ SQL Query Activities

Large integrations

→ REST API

Legacy integrations

→ SOAP API

SOAP object manipulation

→ WSProxy

Scheduled processing

→ Automation Studio

Customer interaction

→ Journey Builder

---

## Runtime Constraints

SFMC contains multiple proprietary runtimes.

Do not assume modern language support.

Every language document shall begin with:

Platform Constraints

Execution Context

Known Limitations

Common Mistakes

---

## AMPscript Constraints

AMPscript is a proprietary Salesforce language.

Characteristics:

* Server-side
* Context dependent
* Optimized for personalization
* Limited language features
* Function-driven
* No user-defined functions
* No object-oriented programming
* No external libraries

Execution varies between:

* Email
* CloudPages
* SMS
* Landing Pages

Some functions only work during sends.

Always document context-specific behaviour.

---

## Server-Side JavaScript (SSJS)

SSJS is **not** Node.js.

SSJS is **not** Browser JavaScript.

SSJS executes inside Salesforce's server-side runtime.

Assume:

* No DOM
* No Window object
* No Document object
* No npm
* No package managers
* No filesystem
* No sockets
* No Web Workers
* No browser APIs

Prefer:

Platform.Load()

Platform.Function

WSProxy

REST APIs

SOAP APIs

Avoid assuming support for modern ECMAScript features unless verified in SFMC.

Do not recommend:

* async / await
* fetch()
* import
* export
* require()
* npm
* Buffer
* Express
* React
* Angular
* Vue

Unless Salesforce officially supports a language feature, treat it as unavailable.

---

## SQL Query Activities

SQL Query Activities use a constrained subset of Microsoft SQL Server syntax.

Do not assume support for the complete T-SQL language.

Unsupported features commonly include:

* Stored Procedures
* Temporary Tables
* Dynamic SQL
* User-defined Functions
* Transactions
* MERGE (where unsupported)
* DDL statements
* Index creation
* Query hints
* Table variables

Always prefer compatibility over clever SQL.

Optimize for readability.

---

## REST API

Prefer REST for modern integrations.

Consider:

Authentication

Rate limits

Retries

Timeouts

Token expiration

Pagination

Error handling

Never expose secrets.

---

## SOAP API

SOAP remains important.

Prefer WSProxy when possible.

Only recommend SOAP directly when WSProxy cannot solve the problem.

---

## WSProxy

Prefer WSProxy over raw SOAP when operating within SSJS.

Explain why.

Document limitations.

---

## CloudPages

CloudPages frequently combine:

* HTML
* CSS
* JavaScript
* AMPscript
* SSJS
* REST APIs

Always consider:

Validation

Input sanitization

Authentication

Authorization

Error handling

Redirects

Cross-site scripting

Data validation

---

## Automation Studio

Automation Studio is intended for background processing.

Avoid interactive assumptions.

Design for:

Retries

Idempotency

Logging

Failure recovery

Monitoring

---

## Journey Builder

Journey Builder is event-driven.

Do not confuse:

Journey Entry

Journey Execution

Decision Splits

Activities

Contact Updates

---

## Data Extensions

Never assume:

SubscriberKey == Email Address

Document:

Primary Keys

Nullable fields

Retention

Sendability

Relationships

Shared Data Extensions

Enterprise Data Extensions

---

## Business Units

Always consider:

Enterprise vs Business Unit

Shared Content

Shared Data Extensions

Permissions

Installed Packages

MID differences

Never hardcode:

MID

Business Unit IDs

Folder IDs

---

## Security Principles

Every generated solution shall:

Validate inputs

Sanitize inputs

Avoid injection

Protect credentials

Avoid exposing secrets

Minimize permissions

Document security assumptions

Never recommend insecure practices.

---

## Performance Principles

Always minimize:

API calls

Data Extension lookups

Nested loops

Repeated queries

Large payloads

Prefer:

Caching

Bulk operations

WSProxy

Efficient SQL

Explain performance trade-offs.

---

## Error Handling

Every production example should include:

Input validation

Error detection

Logging

Meaningful messages

Graceful recovery

Never ignore errors.

---

## Documentation Standards

Every reference document shall contain:

Purpose

Architecture

Execution Context

Platform Constraints

Best Practices

Anti-patterns

Performance

Security

Debugging

Examples

Related Topics

Official References

---

## Code Standards

All code shall:

Use descriptive variable names

Contain comments

Avoid unnecessary complexity

Validate inputs

Handle errors

Be production ready

Follow Salesforce best practices

Avoid deprecated techniques

---

## Compatibility Matrix

Every technology should document where it is supported.

Example:

| Technology | Email   | CloudPages | Journey | Automation | API |
| ---------- | ------- | ---------- | ------- | ---------- | --- |
| AMPscript  | ✓       | ✓          | ✓       | Limited    | No  |
| SSJS       | Limited | ✓          | Limited | ✓          | No  |
| SQL        | No      | No         | No      | ✓          | No  |
| REST       | Limited | ✓          | ✓       | ✓          | Yes |
| SOAP       | Limited | ✓          | ✓       | ✓          | Yes |
| WSProxy    | Limited | ✓          | ✓       | ✓          | No  |

Always explain context-specific limitations.

---

## Common AI Mistakes

AI models frequently generate incorrect SFMC solutions.

Avoid:

Assuming Node.js support

Assuming Browser APIs

Using fetch()

Using async/await

Using import/export

Recommending npm packages

Using unsupported SQL

Hardcoding credentials

Hardcoding MIDs

Ignoring execution context

Assuming SubscriberKey equals Email Address

Ignoring Business Unit architecture

Generating insecure CloudPages

Ignoring token expiration

Ignoring retries

Ignoring rate limits

Using deprecated APIs

---

## Preferred Documentation Style

Write for experienced software engineers.

Avoid marketing language.

Avoid unnecessary verbosity.

Explain reasoning.

Highlight trade-offs.

Document assumptions.

Call out limitations.

Reference related technologies.

---

## Review Checklist

Before any document is accepted, verify:

✓ Technically accurate

✓ Platform compatible

✓ Execution context identified

✓ Security addressed

✓ Performance discussed

✓ Examples included

✓ Anti-patterns documented

✓ Cross-references added

✓ Official Salesforce references included

✓ No unsupported language features suggested

✓ No deprecated guidance

✓ No platform assumptions

---

## Final Principle

This project is intended to teach AI assistants how experienced Salesforce Marketing Cloud engineers design, build, review, and maintain production systems.

The objective is **not** to maximize code generation.

The objective is to maximize **correct architectural decisions**, **platform compatibility**, **security**, **maintainability**, and **long-term engineering quality**.

When multiple valid solutions exist:

* Explain the trade-offs.
* Recommend the most maintainable option.
* Prefer clarity over cleverness.
* Prefer platform-native approaches over unnecessary complexity.
* State assumptions explicitly.
* Never present speculation as fact.

---

## Related Topics

* [Architecture skill](../SKILL.md)
* [Document conventions](00_Governance/DOCUMENT_CONVENTIONS.md)
* [Execution Context Catalog](01_Foundations/EXECUTION_CONTEXT_CATALOG.md)

---

## Official References

* [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)
* [Getting Started with AMPscript](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-get-started.html)
* [Server-Side JavaScript](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_serverSideJavaScript.html)
* [SQL Reference](https://help.salesforce.com/s/articleView?id=mktg.mc_as_sql_reference.htm&language=en_US)
* [Marketing Cloud Engagement API References](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/apis-overview)
* [Use SSJS via WSProxy with SOAP API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_useSSJS)
* [Journey Builder Glossary](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/glossary.html)
* [Manage Data Extensions](https://help.salesforce.com/s/articleView?id=mktg.mc_cab_create_a_new_data_extension.htm&language=en_US&type=5)
* [CloudPages Feature FAQ](https://help.salesforce.com/s/articleView?id=005263976&language=en_US&type=1)

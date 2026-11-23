# Technology Selection Guide

## Purpose

Select the simplest supported technology for Salesforce Marketing Cloud Engagement (SFMC) after execution context is known.

## Hierarchy

1. Native SFMC configuration for static or declarative needs.
2. AMPscript for simple, bounded personalization.
3. SQL Query Activities for set-based transformation.
4. SSJS for supported server-side procedural work.
5. Automation Studio for scheduling and background orchestration.
6. Journey Builder for customer event orchestration.
7. REST API for modern external integration.
8. WSProxy for supported SOAP object work from SSJS.
9. Direct SOAP only where REST or WSProxy cannot meet the need.
10. External processing for large, long-running, or unsuitable SFMC workloads.

## Selection criteria

Evaluate execution context, data volume, latency, security, Business Unit boundary, entitlement, error recovery, monitoring, and ownership. Document a deviation when a more complex tool is selected over a simpler supported option.

## Related Topics

- [Execution Context Catalog](EXECUTION_CONTEXT_CATALOG.md)
- [Security, Performance, and Operations](SECURITY_PERFORMANCE_AND_OPERATIONS.md)
- [Architecture skill](../../SKILL.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

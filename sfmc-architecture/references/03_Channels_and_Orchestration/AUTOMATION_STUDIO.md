# Automation Studio

## Purpose

Design reliable scheduled and background processing.

## Architecture

An automation is a recoverable pipeline, not an interactive workflow. Use distinct stages for ingestion, validation, transformation, publication, and notification. Each stage has an owner, input contract, output contract, run metric, and recovery action.

## Principles

- Prefer SQL Query Activities for set-based transformation.
- Use Script Activities only for bounded procedural work.
- Prevent overlapping processes against the same high-volume Data Extensions.
- Make writes idempotent; use staging and quarantine outputs where needed.
- Stagger schedules, monitor failures, and define restart conditions.
- Treat repeated failures as a root-cause problem, not a retry opportunity.

## Related Topics

- [SQL Query Activities Reference](../02_Runtimes/SQL_QUERY_ACTIVITIES_REFERENCE.md)
- [Scheduled Data Pipelines](../06_Solution_Patterns/SCHEDULED_DATA_PIPELINES.md)
- [Security, Performance, and Operations](../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)

## Official References

- [Retrieving and Segmenting Data with a SQL Query Activity](https://help.salesforce.com/s/articleView?id=sf.mc_as_using_the_query_activity.htm&language=en_US&type=5)

# Reusable Examples

Use examples as implementation blueprints, not copy-and-paste authorization to bypass architecture review.

## Asset contract

Every example identifies:

- Execution context and supported runtime.
- Business problem and required data contract.
- Required permissions, configuration, and Business Unit assumptions.
- Input validation, error behavior, security controls, and scale limits.
- What must be changed before production use.

## Use rules

- Confirm current capability in the relevant detailed reference before use.
- Replace placeholder Data Extension names, IDs, endpoints, and logging paths through controlled configuration.
- Do not copy secrets, customer data, or tenant-specific IDs into reusable code.
- Keep heavy transformations upstream of email and CloudPages rendering.
- Test happy path, missing data, malformed input, duplicates, permissions, and dependency failures.

## Catalog

- [AMPscript Examples](AMPSCRIPT_EXAMPLES.md)
- [SSJS Examples](SSJS_EXAMPLES.md)
- [SQL Query Examples](SQL_QUERY_EXAMPLES.md)
- [CloudPages Examples](CLOUDPAGES_EXAMPLES.md)
- [Automation and Journey Examples](AUTOMATION_AND_JOURNEY_EXAMPLES.md)
- [Integration Examples](INTEGRATION_EXAMPLES.md)

## Related Topics

- [Platform facts](../../SFMC_PLATFORM_FACTS.md)
- [Architecture skill](../../../SKILL.md)
- [Reusable Snippets](../snippets/README.md)

## Official References

- [Marketing Cloud Engagement API and programmatic language overview](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html)

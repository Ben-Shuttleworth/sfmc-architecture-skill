# AMPscript Syntax Style Guide

## Platform Constraints

AMPscript is proprietary, server-side, function-driven, and context-dependent. This guide defines readable syntax style only; it does not expand supported functions, execution contexts, or platform limits.

## Execution Context

Apply these conventions only in a verified AMPscript context: supported email, CloudPages, landing page, SMS, or mobile content. State the specific context before introducing executable code.

## Known Limitations

AMPscript has no user-defined functions, classes, modules, or external libraries. Do not use syntax style to conceal complex application logic that belongs in SQL Query Activities, Automation Studio, SSJS, or an external service.

## Common Mistakes

- Mixing simple inline output with multi-step control flow in one expression.
- Reusing vague variables such as `@value` or `@result` for unrelated business values.
- Using inconsistent casing rather than the library conventions.
- Placing data writes or expensive lookups inside rendering loops.

## Purpose

Make AMPscript consistent, reviewable, and safe to maintain while preserving the library's context, security, performance, and configuration rules.

## Syntax Conventions

- Use `%%[ ... ]%%` blocks for declarations, assignments, conditions, loops, and other multi-step logic. Use `%%=V(@variable)=%%` only for simple rendered output.
- Use UPPERCASE for AMPscript language keywords and declarations, including `VAR`, `SET`, `IF`, `ELSEIF`, `ELSE`, `ENDIF`, `FOR`, `NEXT`, `WHILE`, `THEN`, `DO`, `NOT`, `AND`, and `OR`.
- Declare variables at the start of the block. Name them with `@camelCase` and one business meaning per variable.
- Use PascalCase for AMPscript function names, preserving Salesforce acronym blocks in documented function names, such as `AttributeValue()`, `Empty()`, `LookupRows()`, `RowCount()`, `Row()`, `Field()`, and `CloudPagesURL()`.
- Use double-quoted string literals. Put one declaration or assignment on each line.
- Indent the body of `IF` and `FOR` blocks by two spaces. Align `ELSEIF`, `ELSE`, `ENDIF`, and `NEXT` with the opening statement.
- Use `/* ... */` comments sparingly to explain a context guard, fallback, data contract, or non-obvious platform behavior; do not restate the code.

```ampscript
%%[
VAR @firstName, @greetingName

SET @firstName = AttributeValue("FirstName")
SET @greetingName = "Customer"

IF NOT Empty(@firstName) THEN
  SET @greetingName = ProperCase(@firstName)
ENDIF
]%%

Hello %%=V(@greetingName)=%%,
```

**Context:** Email rendering with a prepared optional `FirstName` recipient attribute.  
**Input and output:** Renders a greeting with a controlled fallback.  
**Security and performance:** Uses no request input, data write, lookup, or external dependency.  
**Error behavior:** Missing `FirstName` uses the fallback; no send error is raised.

## Structure and Data Access

- Keep one block focused on one bounded rendering decision. Extract preparation and multi-step data work to the appropriate upstream context.
- Retrieve a record set once, test `RowCount()`, then select the required row and fields. Do not repeat the same lookup inside a loop or content block.
- Keep configuration values separate from business logic. Do not embed tenant-specific Data Extension names, page IDs, MIDs, folder IDs, or environment-specific values in reusable code.
- Use variables to make the data contract clear; use Data Extension field names exactly as designed.

## Security

- Treat `RequestParameter()` and `QueryParameter()` values as untrusted. Validate them before use and do not render them without output-context controls.
- Guard state-changing operations with `_messagecontext` and an idempotency design. A preview or validation context MUST NOT create unintended records.
- Never place secrets, access tokens, or sensitive data in AMPscript output, URLs, comments, or diagnostics.

## Performance

- Keep message-time logic to bounded formatting, conditions, and a small number of controlled lookups.
- Use SQL Query Activities or upstream services for segmentation, aggregation, recommendations, and large transformations.
- Avoid nested loops, repeated lookups, retry loops, and external orchestration during rendering.

## Related Topics

- [AMPscript Reference](AMPSCRIPT_REFERENCE.md)
- [Detailed AMPscript Reference](../deep_dives/ampscript.md)
- [Style Guide](../00_Governance/STYLE_GUIDE.md)
- [Personalization](../06_Solution_Patterns/PERSONALIZATION.md)
- [AMPscript Examples](../07_Reusable_Assets/examples/AMPSCRIPT_EXAMPLES.md)
- [AMPscript Snippets](../07_Reusable_Assets/snippets/AMPSCRIPT_SNIPPETS.md)

## Official References

- [Basic AMPscript Syntax](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics.html)
- [Case Sensitivity](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-case-sensitivity.html)
- [Order of Operations](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-order-of-operations.html)
- [Message Context Variable](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-personalization-strings-message-context.html)

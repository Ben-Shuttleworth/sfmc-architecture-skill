# AMPscript

## Platform Constraints

AMPscript is a proprietary, interpreted, server-side language for Salesforce Marketing Cloud Engagement. It is function-driven, context-dependent, and intended primarily for personalization and content logic. It has no user-defined functions, object-oriented model, or external-library support.

Do not treat AMPscript as JavaScript, SQL, or a general-purpose application runtime. Use only documented functions, and confirm function support in the target context before use. [Platform facts](../SFMC_PLATFORM_FACTS.md) take precedence over this reference.

## Execution Context

AMPscript can be evaluated when an email or SMS is rendered, when a CloudPage or landing page loads, and in supported mobile-message contexts. Evaluation timing and available values vary by context.

- **Email:** Use for bounded personalization, conditional content, and approved send-time behavior.
- **CloudPages and landing pages:** Use for request values, controlled content rendering, and simple server-side page logic.
- **SMS and mobile:** Use only functions verified for the specific channel and account capability.

Use `_messagecontext` when behavior must differ between sends, previews, validation, landing pages, or CloudPages. Do not perform data-changing actions during preview or validation unless that behavior is explicitly intended and controlled.

## Known Limitations

- No user-defined functions, classes, modules, packages, filesystem access, or external libraries.
- Function availability and output can differ by channel and rendering context.
- Email rendering is not a suitable place for large data processing, unbounded loops, or external integration orchestration.
- AMPscript data access is not a replacement for set-based segmentation or transformation in SQL Query Activities.
- `RaiseError()` affects email processing; it is an error control, not a segmentation mechanism.
- Encrypted CloudPages query strings reduce exposure of parameter values but do not provide authorization by themselves.
- Rendering order matters in email: preheader, HTML body, text body, then subject line.

## Common Mistakes

- Assuming `SubscriberKey` is an email address.
- Using an email preview as proof that send-time behavior is safe.
- Writing to Data Extensions without guarding against preview, validation, or replay contexts.
- Repeating Data Extension lookups inside loops or content blocks.
- Rendering request parameters directly into HTML, links, or redirects.
- Hardcoding Data Extension names, page IDs, MIDs, folder IDs, or tenant-specific values without configuration ownership.
- Using AMPscript for scheduled ETL, large integrations, or complex reusable business logic.
- Raising send errors to implement audience segmentation.

## Purpose

Use AMPscript to produce relevant, safe content at the point Marketing Cloud Engagement renders a message or page. Typical uses include recipient attribute access, conditional content, controlled Data Extension lookups, locale-aware formatting, and secure generation of CloudPages links.

Choose SQL Query Activities and Automation Studio for bulk data preparation. Choose SSJS only when supported server-side procedural logic is genuinely required. Keep email-time AMPscript small, predictable, and dependent on prepared data.

## Architecture

Use a prepared-personalization architecture:

1. Define the recipient identity and source attributes.
2. Prepare segmentation, eligibility, and expensive calculations upstream.
3. Render only the data needed for the current recipient.
4. Apply approved fallback values for absent optional data.
5. Guard side effects by execution context.
6. Log or surface failures through the context-appropriate operational mechanism.

For CloudPages, separate request capture, validation, authorization, persistence, and response rendering. Treat URL and form parameters as untrusted. A CloudPages link carrying encrypted values is a transport mechanism, not proof that the requester is authorized to act.

## Best Practices

- Declare the target context and the required data contract before writing AMPscript.
- Retrieve recipient values with `AttributeValue()` and test optional values with `Empty()` or documented null-handling functions.
- Use one clearly named variable per business value; AMPscript variables use the `@camelCase` convention and function names use PascalCase.
- Prefer a single lookup followed by `RowCount()`, `Row()`, and `Field()` over repeated lookups for the same record.
- Define a safe fallback for missing optional personalization.
- Use `CloudPagesURL()` for links that must carry protected parameter values; validate every received value on the destination page.
- Use external keys and configuration where asset references must survive deployment or Business Unit changes.
- Use `_messagecontext` to prevent unintended side effects in preview and validation.
- Keep personalization logic close to the rendered content, but keep expensive decisioning upstream.
- Comment why a context guard, fallback, lookup, or error condition exists.

## Anti-patterns

| Anti-pattern | Why it fails | Preferred approach |
|---|---|---|
| Full business workflow in an email | Send-time execution is bounded and difficult to recover. | Prepare data upstream; render a small result. |
| Write on every preview or validation | Testing can create misleading or duplicate records. | Guard writes with `_messagecontext`. |
| Direct output of request data | Creates injection and data-exposure risk. | Validate, sanitize, and encode for the output context. |
| Lookup inside a loop | Increases Data Extension calls and rendering latency. | Retrieve once; iterate only over the returned rowset when necessary. |
| `RaiseError()` for filtering | Can interrupt sends and obscures segmentation intent. | Use segmentation, exclusions, or eligibility data upstream. |
| Email-time remote dependency | Adds latency and failure risk during rendering. | Synchronize or stage required data before the send. |

## Performance

- Keep email-time logic limited to recipient-level formatting, conditional display, and a small number of bounded lookups.
- Prepare eligibility, aggregates, recommendations, and audience calculations in SQL Query Activities or upstream services.
- Avoid nested loops and repeated calls to the same Data Extension.
- Retrieve only fields required for rendering.
- Use a clear fallback when lookup data is absent; do not add retry loops during rendering.
- Test with representative data volumes, sparse attributes, and the intended send context.

## Security

- Treat `RequestParameter()` and `QueryParameter()` values as untrusted input.
- Validate required values for presence, format, length, and allowed values before using them.
- Do not render untrusted values into HTML, JavaScript, URLs, or redirects without context-appropriate handling.
- Do not expose secrets, access tokens, internal identifiers, or sensitive data in output, URLs, or error messages.
- Use `CloudPagesURL()` where encrypted parameter transport is appropriate, then independently validate and authorize the request.
- Enforce consent and channel eligibility upstream; AMPscript must not bypass suppression or preference controls.
- Use `RaiseError()` only when halting processing is the correct integrity or compliance outcome, and document its send impact.

## Debugging

1. Confirm the actual execution context with `_messagecontext`.
2. Verify the recipient data contract: identity key, field names, Data Extension access, nullability, and expected values.
3. Test missing, malformed, and duplicate data—not only the happy path.
4. Verify email component order when variables are assigned across preheader, HTML, text, and subject content.
5. Check that side-effecting logic is excluded from preview and validation contexts.
6. Reduce the code to a single lookup or condition, then restore steps incrementally.
7. For CloudPages, validate inbound parameters and authorization independently of encrypted query-string transport.
8. Use controlled diagnostic output only in non-production or restricted testing paths; remove or disable it before release.

## Examples

### Safe optional email personalization

**Context:** Email rendering.  
**Inputs:** `FirstName` recipient attribute.  
**Behavior:** Renders a safe business fallback when the optional attribute is absent.

```ampscript
%%[
VAR @firstName, @greetingName

SET @firstName = AttributeValue("FirstName")

IF Empty(@firstName) THEN
  SET @greetingName = "Customer"
ELSE
  SET @greetingName = ProperCase(@firstName)
ENDIF
]%%

Hello %%=V(@greetingName)=%%,
```

Do not use this pattern to output untrusted form or URL input without output-context controls.

### Bounded preference lookup with fallback

**Context:** Email rendering.  
**Inputs:** `SubscriberKey`; `CustomerPreferences` Data Extension with a documented key.  
**Behavior:** Uses a default when no approved preference record is found.

```ampscript
%%[
VAR @subscriberKey, @preferenceRows, @preferenceRow, @preferredCategory

SET @subscriberKey = AttributeValue("SubscriberKey")
SET @preferredCategory = "General"

IF NOT Empty(@subscriberKey) THEN
  SET @preferenceRows = LookupRows("CustomerPreferences", "SubscriberKey", @subscriberKey)

  IF RowCount(@preferenceRows) > 0 THEN
    SET @preferenceRow = Row(@preferenceRows, 1)
    SET @preferredCategory = Field(@preferenceRow, "PreferredCategory")
  ENDIF
ENDIF
]%%
```

The Data Extension design must guarantee the intended key behavior. Do not assume a returned first row represents a deterministic business choice when duplicates are permitted.

### Context guard for send-only side effects

**Context:** Email rendering.  
**Inputs:** A controlled operation that is valid only for an actual send.  
**Behavior:** Prevents the operation from running during preview and validation.

```ampscript
%%[
IF _messagecontext == "SEND" THEN
  /* Perform only an approved, idempotent send-time operation here. */
ENDIF
]%%
```

Before adding a write, document its primary key, replay behavior, error behavior, and operational owner. Prefer upstream processing whenever possible.

## Related Topics

- [Platform facts](../SFMC_PLATFORM_FACTS.md)
- [Architecture skill](../../SKILL.md)
- [AMPscript Syntax Style Guide](../02_Runtimes/AMPSCRIPT_SYNTAX_STYLE_GUIDE.md)
- [Execution Context Catalog](../01_Foundations/EXECUTION_CONTEXT_CATALOG.md)
- [Technology Selection Guide](../01_Foundations/TECHNOLOGY_SELECTION_GUIDE.md)
- [Security, Performance, and Operations](../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)
- [Email Content and Delivery](../03_Channels_and_Orchestration/EMAIL_CONTENT_AND_DELIVERY.md)
- [CloudPages Reference](../02_Runtimes/CLOUDPAGES_REFERENCE.md)
- [Personalization](../06_Solution_Patterns/PERSONALIZATION.md)
- [AMPscript Examples](../07_Reusable_Assets/examples/AMPSCRIPT_EXAMPLES.md)
- [AMPscript Snippets](../07_Reusable_Assets/snippets/AMPSCRIPT_SNIPPETS.md)

## Official References

- [Getting Started with AMPscript](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-get-started.html)
- [Basic AMPscript Syntax](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics.html)
- [Built-In Variables](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-personalization-strings.html)
- [Message Context Variable](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-personalization-strings-message-context.html)
- [Order of Operations](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-guide-language-basics-order-of-operations.html)
- [AMPscript Function Reference](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/references)
- [Site Functions](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/references/mc-ampscript-sites)
- [RaiseError()](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/references/mc-ampscript-utilities/mc-ampscript-reference-utilities-raise-error.html)

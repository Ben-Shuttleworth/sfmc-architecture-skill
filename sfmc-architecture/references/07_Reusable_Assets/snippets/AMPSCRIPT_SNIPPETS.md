# AMPscript Snippets

Use these fragments with the [required use checks](README.md#required-use-checks) and [AMPscript reference](../../deep_dives/ampscript.md). Unless stated otherwise, they omit deployment configuration, complete validation, error handling, logging, monitoring, and release controls.

## Optional attribute fallback

**Context:** Supported message or page rendering.  
**Input:** Optional FirstName.

    %%[
    VAR @firstName

    SET @firstName = AttributeValue("FirstName")
    IF Empty(@firstName) THEN
      SET @firstName = "Customer"
    ENDIF
    ]%%

## Message-context guard

**Context:** Email rendering.  
**Use:** Prevent an approved side effect during preview and validation.

    %%[
    IF _messagecontext == "SEND" THEN
      /* Approved idempotent operation only. */
    ENDIF
    ]%%

## Single keyed lookup

**Context:** Supported rendering context.  
**Input:** A declared and populated `@subscriberKey`; `CustomerPreferences` has the intended key behavior.

    %%[
    VAR @subscriberKey, @rows, @row, @value

    SET @rows = LookupRows("CustomerPreferences", "SubscriberKey", @subscriberKey)
    IF RowCount(@rows) > 0 THEN
      SET @row = Row(@rows, 1)
      SET @value = Field(@row, "PreferredCategory")
    ENDIF
    ]%%

For lookup limits and input handling, follow the [AMPscript reference](../../deep_dives/ampscript.md).

## Related Topics

- [AMPscript reference](../../deep_dives/ampscript.md)

## Official References

- [AMPscript Function Reference](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/references)

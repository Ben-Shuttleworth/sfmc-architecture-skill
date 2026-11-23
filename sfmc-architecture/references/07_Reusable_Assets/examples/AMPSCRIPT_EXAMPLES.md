# AMPscript Examples

Use these implementation blueprints with the [example asset contract](README.md#asset-contract) and the supported contexts and controls in the [AMPscript reference](../../deep_dives/ampscript.md).

## Safe optional email personalization

**Context:** Email rendering.  
**Data contract:** Optional FirstName recipient attribute.  
**Outcome:** Displays a controlled fallback without a Data Extension lookup.

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

Apply the input and rendering constraints in the [AMPscript reference](../../deep_dives/ampscript.md); this example is limited to prepared recipient data.

## Bounded preference lookup

**Context:** Email rendering.  
**Data contract:** CustomerPreferences contains one approved record per SubscriberKey.  
**Outcome:** Uses a default category when no preference record is available.

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

The source Data Extension must enforce the intended uniqueness rule. Prepare complex recommendations upstream.

For a reusable send guard, use the [Message-context guard](../snippets/AMPSCRIPT_SNIPPETS.md#message-context-guard) with the side-effect constraints in the [AMPscript reference](../../deep_dives/ampscript.md).

## Related Topics

- [AMPscript reference](../../deep_dives/ampscript.md)
- [Personalization pattern](../../06_Solution_Patterns/PERSONALIZATION.md)

## Official References

- [Getting Started with AMPscript](https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/guide/mc-ampscript-get-started.html)

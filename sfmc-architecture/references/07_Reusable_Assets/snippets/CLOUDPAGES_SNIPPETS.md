# CloudPages Snippets

Use these fragments only with the [required use checks](README.md#required-use-checks), [CloudPages Reference](../../02_Runtimes/CLOUDPAGES_REFERENCE.md), and [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md). Unless stated otherwise, they omit the complete request flow, configuration resolution, abuse controls, logging, monitoring, and release controls.

## Controlled POST validation

**Context:** CloudPages POST handler after HTTPS enforcement, request-attempt limiting, and honeypot evaluation. The GET path must issue a short-lived form token and must not persist consent.  
**Safe reuse:** Keep the field names and consent value synchronized with the rendered form. Reject before any consent write.

    function asText(value) {
      return value === null || value === undefined ? "" : String(value);
    }

    function trim(value) {
      return asText(value).replace(/^\s+|\s+$/g, "");
    }

    var method = asText(Platform.Request.Method).toUpperCase();
    var emailAddress = trim(Platform.Request.GetFormField("emailAddress")).toLowerCase();
    var consent = asText(Platform.Request.GetFormField("consent"));
    var formToken = trim(Platform.Request.GetFormField("formToken"));

    if (
      !Platform.Request.IsSSL ||
      method !== "POST" ||
      emailAddress === "" ||
      emailAddress.length > 254 ||
      !Platform.Function.IsEmailAddress(emailAddress) ||
      consent !== "yes" ||
      !/^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$/.test(formToken)
    ) {
      throw new Error("VALIDATION_REJECTED");
    }

Use only inside the canonical [production newsletter consent capture](../examples/CLOUDPAGES_EXAMPLES.md#production-newsletter-consent-capture) flow.

## Conditional one-time token consumption

**Context:** CloudPages POST after the token record has been retrieved and its `ExpiresAtEpoch` verified.  
**Safe reuse:** `CloudPageFormToken` must have `FormToken` as its primary key. Only a transition from `Issued` to `Consumed` permits persistence.

    var updated = Platform.Function.UpdateData(
      "CloudPageFormToken",
      ["FormToken", "FormCode", "Status"],
      [formToken, "NEWSLETTER_SIGNUP", "Issued"],
      ["Status", "ConsumedAt"],
      ["Consumed", Platform.Function.Now()]
    );

    if (updated !== 1) {
      throw new Error("DUPLICATE_OR_INVALID_TOKEN");
    }

Use the consumed `formToken` as the primary-key `SubmissionId` in the canonical capture flow.

## Consent evidence persistence

**Context:** CloudPages POST after successful validation and one-time token consumption.  
**Safe reuse:** The consent copy shown to the visitor must exactly match `consentText`; the version must change whenever that copy changes.

    var consentText = "I agree to receive the monthly newsletter by email. I can unsubscribe at any time.";
    var inserted = Platform.Function.InsertData(
      "NewsletterConsentEvent",
      [
        "SubmissionId",
        "CorrelationId",
        "EmailAddress",
        "PublicationCode",
        "ConsentStatus",
        "ConsentTextVersion",
        "ConsentText",
        "Source",
        "SubmittedAt",
        "ClientIP",
        "UserAgent"
      ],
      [
        formToken,
        correlationId,
        emailAddress,
        "MONTHLY_NEWSLETTER",
        "OptInRequested",
        "newsletter-opt-in-v1",
        consentText,
        "CLOUDPAGE_NEWSLETTER_SIGNUP",
        Platform.Function.Now(),
        clientIP,
        asText(Platform.Request.UserAgent).substring(0, 500)
      ]
    );

    if (inserted !== 1) {
      throw new Error("CONSENT_PERSISTENCE_FAILED");
    }

Apply the persistence and confirmation requirements in [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md).

## Encrypted parameter link

**Context:** AMPscript-generated CloudPages link.

    %%[
    VAR @pageUrl

    SET @pageUrl = CloudPagesURL(12345, "subscriberKey", AttributeValue("SubscriberKey"))
    ]%%

    <a href="%%=RedirectTo(@pageUrl)=%%">Manage preferences</a>

Replace the page identifier through deployment configuration. Encrypted parameters do not authorize the visitor.

## Safe generic failure output

**Context:** CloudPages server-side catch path.

    Platform.Response.Write("Unable to process your request.");

Log a correlation ID and safe category internally; never return exception details or secrets.

## Related Topics

- [CloudPages Reference](../../02_Runtimes/CLOUDPAGES_REFERENCE.md)
- [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md)

## Official References

- [GetFormField](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_utilitiesGetFormField.html)

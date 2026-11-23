# CloudPages Examples

Use these implementation blueprints with the [example asset contract](README.md#asset-contract), [CloudPages Reference](../../02_Runtimes/CLOUDPAGES_REFERENCE.md), and [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md).

## Production newsletter consent capture

**Context:** Public HTTPS CloudPage. GET issues a short-lived, one-time form token; POST validates and consumes it before recording consent.  
**Inputs:** Email address, affirmative consent checkbox, form token, and an empty honeypot field.  
**Data contract:** Use restricted, non-sendable Data Extensions with these fields:

- `CloudPageFormToken`: `FormToken` Text(36) primary key, `FormCode` Text(40), `Status` Text(12), `IssuedAt` Date, `ExpiresAtEpoch` Text(13), and `ConsumedAt` Date. Enable one-day row-based retention.
- `CloudPageAbuseWindow`: composite primary key of `FormCode` Text(40) and `ClientIP` Text(45), plus `AttemptCount` Number, `WindowExpiresAtEpoch` Text(13), and `UpdatedAt` Date. Enable one-day row-based retention.
- `NewsletterConsentEvent`: `SubmissionId` Text(36) primary key, `CorrelationId` Text(36), `EmailAddress` EmailAddress(254), `PublicationCode` Text(40), `ConsentStatus` Text(20), `ConsentTextVersion` Text(40), `ConsentText` Text(500), `Source` Text(40), `SubmittedAt` Date, `ClientIP` Text(45), and `UserAgent` Text(500). Apply an approved consent-evidence retention policy.
- `CloudPageOperationalLog`: `CorrelationId` Text(36) primary key, `FormCode` Text(40), `Category` Text(40), and `OccurredAt` Date. Apply the operational log retention policy.

**Configuration:** Keep the non-secret policy values and Data Extension names in the configuration boundary at the start of the script. Do not put credentials, tokens, or environment secrets in page source.  
**Outcome:** Rejects unsupported methods and non-HTTPS requests, limits request volume, validates server-side, prevents form replay, records affirmative consent evidence, and returns no diagnostics to the visitor.

    <script runat="server">
    Platform.Load("Core", "1.1");

    var CONFIG = {
      formCode: "NEWSLETTER_SIGNUP",
      publicationCode: "MONTHLY_NEWSLETTER",
      source: "CLOUDPAGE_NEWSLETTER_SIGNUP",
      consentStatus: "OptInRequested",
      consentTextVersion: "newsletter-opt-in-v1",
      consentText: "I agree to receive the monthly newsletter by email. I can unsubscribe at any time.",
      tokenLifetimeMs: 900000,
      attemptWindowMs: 600000,
      maxAttemptsPerWindow: 20,
      tokenDE: "CloudPageFormToken",
      abuseWindowDE: "CloudPageAbuseWindow",
      consentDE: "NewsletterConsentEvent",
      logDE: "CloudPageOperationalLog"
    };

    function asText(value) {
      return value === null || value === undefined ? "" : String(value);
    }

    function trim(value) {
      return asText(value).replace(/^\s+|\s+$/g, "");
    }

    function writeMessage(message) {
      Platform.Response.Write(
        "<!doctype html><html lang=\"en\"><head><meta charset=\"utf-8\">" +
        "<meta name=\"viewport\" content=\"width=device-width,initial-scale=1\">" +
        "<title>Newsletter signup</title></head><body><main><p>" +
        message +
        "</p></main></body></html>"
      );
    }

    function writeOperationalLog(correlationId, category, occurredAt) {
      try {
        Platform.Function.InsertData(
          CONFIG.logDE,
          ["CorrelationId", "FormCode", "Category", "OccurredAt"],
          [correlationId, CONFIG.formCode, category, occurredAt]
        );
      } catch (ignored) {
        /* The visitor response must not disclose either failure. */
      }
    }

    function isRateLimited(clientIP, nowEpoch) {
      var windows = Platform.Function.LookupRows(
        CONFIG.abuseWindowDE,
        ["FormCode", "ClientIP"],
        [CONFIG.formCode, clientIP]
      );
      var windowExpiresAt;
      var attemptCount;
      var updated;

      if (windows.length === 0) {
        Platform.Function.InsertData(
          CONFIG.abuseWindowDE,
          ["FormCode", "ClientIP", "AttemptCount", "WindowExpiresAtEpoch", "UpdatedAt"],
          [
            CONFIG.formCode,
            clientIP,
            1,
            String(nowEpoch + CONFIG.attemptWindowMs),
            Platform.Function.Now()
          ]
        );
        return false;
      }

      if (windows.length !== 1) {
        throw new Error("ABUSE_WINDOW_INVALID");
      }

      windowExpiresAt = parseInt(windows[0].WindowExpiresAtEpoch, 10);
      attemptCount = parseInt(windows[0].AttemptCount, 10);

      if (isNaN(windowExpiresAt) || isNaN(attemptCount)) {
        throw new Error("ABUSE_WINDOW_INVALID");
      }

      if (windowExpiresAt <= nowEpoch) {
        updated = Platform.Function.UpdateData(
          CONFIG.abuseWindowDE,
          ["FormCode", "ClientIP", "WindowExpiresAtEpoch"],
          [CONFIG.formCode, clientIP, String(windows[0].WindowExpiresAtEpoch)],
          ["AttemptCount", "WindowExpiresAtEpoch", "UpdatedAt"],
          [1, String(nowEpoch + CONFIG.attemptWindowMs), Platform.Function.Now()]
        );

        if (updated !== 1) {
          throw new Error("ABUSE_WINDOW_CONFLICT");
        }
        return false;
      }

      if (attemptCount >= CONFIG.maxAttemptsPerWindow) {
        return true;
      }

      updated = Platform.Function.UpdateData(
        CONFIG.abuseWindowDE,
        ["FormCode", "ClientIP", "AttemptCount", "WindowExpiresAtEpoch"],
        [
          CONFIG.formCode,
          clientIP,
          attemptCount,
          String(windows[0].WindowExpiresAtEpoch)
        ],
        ["AttemptCount", "UpdatedAt"],
        [attemptCount + 1, Platform.Function.Now()]
      );

      if (updated !== 1) {
        throw new Error("ABUSE_WINDOW_CONFLICT");
      }
      return false;
    }

    function renderForm(formToken) {
      Platform.Response.Write(
        "<!doctype html><html lang=\"en\"><head><meta charset=\"utf-8\">" +
        "<meta name=\"viewport\" content=\"width=device-width,initial-scale=1\">" +
        "<title>Newsletter signup</title></head><body><main>" +
        "<form method=\"post\">" +
        "<input type=\"hidden\" name=\"formToken\" value=\"" + formToken + "\">" +
        "<div aria-hidden=\"true\" style=\"position:absolute;left:-10000px\">" +
        "<label>Website <input type=\"text\" name=\"website\" tabindex=\"-1\" autocomplete=\"off\"></label></div>" +
        "<label>Email <input type=\"email\" name=\"emailAddress\" maxlength=\"254\" required></label>" +
        "<label><input type=\"checkbox\" name=\"consent\" value=\"yes\" required> " +
        CONFIG.consentText +
        "</label><button type=\"submit\">Subscribe</button></form>" +
        "</main></body></html>"
      );
    }

    function handleGet(nowEpoch) {
      var formToken = Platform.Function.GUID();
      var inserted = Platform.Function.InsertData(
        CONFIG.tokenDE,
        ["FormToken", "FormCode", "Status", "IssuedAt", "ExpiresAtEpoch"],
        [
          formToken,
          CONFIG.formCode,
          "Issued",
          Platform.Function.Now(),
          String(nowEpoch + CONFIG.tokenLifetimeMs)
        ]
      );

      if (inserted !== 1) {
        throw new Error("TOKEN_ISSUE_FAILED");
      }
      renderForm(formToken);
    }

    function handlePost(correlationId, clientIP, userAgent, nowEpoch) {
      var emailAddress = trim(Platform.Request.GetFormField("emailAddress")).toLowerCase();
      var consent = asText(Platform.Request.GetFormField("consent"));
      var formToken = trim(Platform.Request.GetFormField("formToken"));
      var honeypot = trim(Platform.Request.GetFormField("website"));
      var tokenRows;
      var tokenRow;
      var updated;
      var inserted;

      if (honeypot !== "") {
        writeOperationalLog(correlationId, "ABUSE_HONEYPOT", Platform.Function.Now());
        writeMessage("Thank you. If the address is eligible, please check your inbox.");
        return;
      }

      if (
        emailAddress === "" ||
        emailAddress.length > 254 ||
        !Platform.Function.IsEmailAddress(emailAddress) ||
        consent !== "yes" ||
        !/^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$/.test(formToken)
      ) {
        writeOperationalLog(correlationId, "VALIDATION_REJECTED", Platform.Function.Now());
        writeMessage("Check the required fields and try again.");
        return;
      }

      failureCategory = "TOKEN_LOOKUP_FAILED";
      tokenRows = Platform.Function.LookupRows(
        CONFIG.tokenDE,
        ["FormToken", "FormCode", "Status"],
        [formToken, CONFIG.formCode, "Issued"]
      );

      if (
        tokenRows.length !== 1 ||
        parseInt(tokenRows[0].ExpiresAtEpoch, 10) <= nowEpoch
      ) {
        writeOperationalLog(correlationId, "TOKEN_REJECTED", Platform.Function.Now());
        writeMessage("This form has expired. Reload the page and try again.");
        return;
      }

      failureCategory = "TOKEN_CONSUMPTION_FAILED";
      updated = Platform.Function.UpdateData(
        CONFIG.tokenDE,
        ["FormToken", "FormCode", "Status"],
        [formToken, CONFIG.formCode, "Issued"],
        ["Status", "ConsumedAt"],
        ["Consumed", Platform.Function.Now()]
      );

      if (updated !== 1) {
        writeOperationalLog(correlationId, "DUPLICATE_REJECTED", Platform.Function.Now());
        writeMessage("Thank you. If the address is eligible, please check your inbox.");
        return;
      }

      failureCategory = "CONSENT_PERSISTENCE_FAILED";
      inserted = Platform.Function.InsertData(
        CONFIG.consentDE,
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
          CONFIG.publicationCode,
          CONFIG.consentStatus,
          CONFIG.consentTextVersion,
          CONFIG.consentText,
          CONFIG.source,
          Platform.Function.Now(),
          clientIP,
          asText(userAgent).substring(0, 500)
        ]
      );

      if (inserted !== 1) {
        throw new Error("CONSENT_PERSISTENCE_FAILED");
      }

      writeMessage("Thank you. Please check your inbox to confirm your subscription.");
    }

    var correlationId = Platform.Function.GUID();
    var failureCategory = "UNEXPECTED_FAILURE";

    try {
      var method = asText(Platform.Request.Method).toUpperCase();
      var clientIP = trim(Platform.Request.ClientIP);
      var nowEpoch = new Date().getTime();

      if (!Platform.Request.IsSSL || clientIP === "") {
        failureCategory = "SECURE_REQUEST_REQUIRED";
        throw new Error(failureCategory);
      }

      if (method !== "GET" && method !== "POST") {
        failureCategory = "METHOD_REJECTED";
        throw new Error(failureCategory);
      }

      failureCategory = "ABUSE_CONTROL_FAILED";
      if (isRateLimited(clientIP, nowEpoch)) {
        writeMessage("Unable to process your request right now. Try again later.");
      } else if (method === "GET") {
        failureCategory = "TOKEN_ISSUE_FAILED";
        handleGet(nowEpoch);
      } else {
        failureCategory = "SUBMISSION_FAILED";
        handlePost(
          correlationId,
          clientIP,
          Platform.Request.UserAgent,
          nowEpoch
        );
      }
    } catch (error) {
      writeOperationalLog(correlationId, failureCategory, Platform.Function.Now());
      writeMessage("Unable to process your request.");
    }
    </script>

The one-time token and primary key prevent replay of the same submission. `NewsletterConsentEvent` is append-only evidence, not a subscriber-status table: a separate, monitored confirmation workflow must verify ownership before activating the subscription. The application-level attempt limit and honeypot are backstops; use approved edge protection for a hostile or high-volume public surface. Restrict access to IP address and user-agent evidence, minimize retention, and document the lawful basis for collecting it.

## Protected parameter handoff

**Context:** Email to CloudPages link.  
**Outcome:** Creates encrypted parameter transport; the destination must still validate and authorize the request.

    %%[
    VAR @pageUrl
    SET @pageUrl = CloudPagesURL(12345, "subscriberKey", AttributeValue("SubscriberKey"))
    ]%%

    <a href="%%=RedirectTo(@pageUrl)=%%">Manage preferences</a>

Replace the page identifier through deployment configuration. Do not treat the encrypted value as a credential or authorize an account change solely from it.

## Related Topics

- [CloudPages Reference](../../02_Runtimes/CLOUDPAGES_REFERENCE.md)
- [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md)

## Official References

- [CloudPages Feature FAQ](https://help.salesforce.com/s/articleView?id=005263976&language=en_US&type=1)
- [GetFormField](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_utilitiesGetFormField.html)

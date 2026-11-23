# API Snippets

Use these fragments with the [required use checks](README.md#required-use-checks), [Integration Reliability](../../04_Integrations/INTEGRATION_RELIABILITY.md), and [SSJS reference](../../deep_dives/ssjs.md). Unless stated otherwise, they omit complete configuration resolution, orchestration, reconciliation, monitoring, and release controls.

## Server-to-server OAuth request

**Context:** Automation Studio Script Activity.  
**Safe reuse:** `authorizationBaseUri`, `clientId`, and `clientSecret` MUST be injected from approved protected configuration. The Authorization Base URI MUST be the tenant-specific HTTPS URI from Installed Packages and end with `/`. Do not persist or log the request body.

    if (
      !clientId ||
      !clientSecret ||
      !/^https:\/\/[a-z0-9-]+\.auth\.marketingcloudapis\.com\/$/i.test(
        authorizationBaseUri
      )
    ) {
      throw new Error("OAUTH_AUTH_BASE_URI_INVALID");
    }

    var tokenPayload = {
      grant_type: "client_credentials",
      client_id: clientId,
      client_secret: clientSecret
    };
    if (targetMid !== null && targetMid !== undefined) {
      if (
        isNaN(Number(targetMid)) ||
        Number(targetMid) < 1 ||
        Number(targetMid) % 1 !== 0
      ) {
        throw new Error("OAUTH_TARGET_MID_INVALID");
      }
      tokenPayload.account_id = Number(targetMid);
    }

    var tokenRequest = new Script.Util.HttpRequest(
      authorizationBaseUri + "v2/token"
    );
    tokenRequest.method = "POST";
    tokenRequest.contentType = "application/json";
    tokenRequest.encoding = "UTF-8";
    tokenRequest.retries = 0;
    tokenRequest.continueOnError = true;
    tokenRequest.postData = Platform.Function.Stringify(tokenPayload);

    var tokenResponse = tokenRequest.send();
    if (
      !tokenResponse ||
      tokenResponse.statusCode < 200 ||
      tokenResponse.statusCode >= 300
    ) {
      throw new Error("OAUTH_TOKEN_REQUEST_FAILED");
    }

    var tokenDocument = Platform.Function.ParseJSON(
      String(tokenResponse.content)
    );
    if (
      !tokenDocument ||
      tokenDocument.token_type !== "Bearer" ||
      !tokenDocument.access_token ||
      String(tokenDocument.access_token).length > 512 ||
      Number(tokenDocument.expires_in) <= 0 ||
      !/^https:\/\/[a-z0-9-]+\.rest\.marketingcloudapis\.com\/?$/i.test(
        tokenDocument.rest_instance_url
      )
    ) {
      throw new Error("OAUTH_TOKEN_RESPONSE_INVALID");
    }

The `account_id` property is optional; omit it when the integration remains in the Installed Package Business Unit. The client-credentials grant returns no refresh token. Reuse the access token within its reported lifetime and use the returned instance URL for API calls.

## Retry classification

**Context:** After a documented HTTP request with `retries = 0`.  
**Safe reuse:** The orchestrator owns delayed retries and preserves the correlation ID and idempotency key.

    var statusCode = Number(response.statusCode);
    var retryable =
      statusCode === 408 ||
      statusCode === 429 ||
      statusCode === 500 ||
      statusCode === 502 ||
      statusCode === 503 ||
      statusCode === 504;

    if (statusCode < 200 || statusCode >= 300) {
      errorCategory = retryable
        ? "DEPENDENCY_TRANSIENT_HTTP_" + statusCode
        : "DEPENDENCY_PERMANENT_HTTP_" + statusCode;
      throw new Error(errorCategory);
    }

Do not busy-wait inside SSJS. Honor `Retry-After` for supported throttling and availability responses, keep attempts bounded, and retry writes only when their idempotency contract makes replay safe.

## Safe operational log

**Context:** Integration outcome logging after sensitive values have been excluded.  
**Safe reuse:** `IntegrationOperationalLog` MUST be restricted, non-sendable, and governed by the operational retention policy.

    var inserted = Platform.Function.InsertData(
      "IntegrationOperationalLog",
      [
        "CorrelationId",
        "IntegrationCode",
        "Operation",
        "AttemptNumber",
        "Outcome",
        "ErrorCategory",
        "HttpStatus",
        "Retryable",
        "OccurredAt"
      ],
      [
        correlationId,
        integrationCode,
        operation,
        attemptNumber,
        outcome,
        errorCategory,
        httpStatus,
        retryable,
        Platform.Function.Now()
      ]
    );
    if (inserted !== 1) {
      throw new Error("OPERATIONAL_LOG_WRITE_FAILED");
    }

Never log client secrets, access tokens, authorization headers, or unrestricted request and response bodies.

## WSProxy batch intent

Use the bounded result-handling and continuation patterns in [SSJS and WSProxy Snippets](SSJS_AND_WSPROXY_SNIPPETS.md#bounded-wsproxy-retrieve).

## Related Topics

- [REST API](../../04_Integrations/REST_API.md)
- [Integration Reliability](../../04_Integrations/INTEGRATION_RELIABILITY.md)
- [SOAP and WSProxy](../../04_Integrations/SOAP_AND_WSPROXY.md)
- [Detailed SSJS reference](../../deep_dives/ssjs.md)

## Official References

- [Server-to-Server Integrations with Client Credentials Grant Type](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/integration-s2s-client-credentials)
- [Use SSJS via WSProxy with SOAP API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_useSSJS)

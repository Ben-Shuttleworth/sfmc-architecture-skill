# Integration Examples

Use these implementation blueprints with the [example asset contract](README.md#asset-contract), [Integration Reliability](../../04_Integrations/INTEGRATION_RELIABILITY.md), and the applicable API reference.

## Server-to-server REST execution

**Context:** Automation Studio Script Activity.  
**Inputs:** `oauthConfig`, `operationConfig`, and an operation-specific response validator supplied to `runRestGet`.  
**Configuration contract:** `oauthConfig` contains the Authorization Base URI, client ID, client secret, and optional target MID and scope. `operationConfig` contains a stable integration code, an approved REST resource path, a correlation ID preserved across retries, the current orchestrated attempt number, and the `IntegrationOperationalLog` Data Extension name.  
**Secure configuration:** Inject the client ID and client secret from approved protected configuration at runtime. Do not store credentials or access tokens in source code, Content Builder, a Data Extension, URLs, or logs.  
**Logging contract:** `IntegrationOperationalLog` is restricted and non-sendable, with a composite primary key of `CorrelationId` Text(36) and `AttemptNumber` Number plus `IntegrationCode` Text(50), `Operation` Text(50), `Outcome` Text(20), `ErrorCategory` Text(50), `HttpStatus` Number, `Retryable` Boolean, `RetryAfter` Text(64), and `OccurredAt` Date.  
**Outcome:** Acquires one client-credentials token for the run, uses the returned REST instance URL, validates the response, and records a safe structured outcome.

    <script runat="server">
    Platform.Load("Core", "1.1");

    function integrationError(category, httpStatus, retryable, retryAfter) {
      return {
        category: category,
        httpStatus: httpStatus || 0,
        retryable: retryable === true,
        retryAfter: retryAfter || ""
      };
    }

    function requiredText(value, category) {
      var text = value === null || value === undefined ? "" : String(value);
      if (text === "") {
        throw integrationError(category, 0, false, "");
      }
      return text;
    }

    function validateAuthorizationBaseUri(value) {
      var uri = requiredText(value, "CONFIG_AUTH_BASE_URI_MISSING");
      if (!/^https:\/\/[a-z0-9-]+\.auth\.marketingcloudapis\.com\/$/i.test(uri)) {
        throw integrationError("CONFIG_AUTH_BASE_URI_INVALID", 0, false, "");
      }
      return uri;
    }

    function validateRestInstanceUrl(value) {
      var uri = requiredText(value, "OAUTH_REST_INSTANCE_URL_MISSING");
      if (!/^https:\/\/[a-z0-9-]+\.rest\.marketingcloudapis\.com\/?$/i.test(uri)) {
        throw integrationError("OAUTH_REST_INSTANCE_URL_INVALID", 0, false, "");
      }
      return uri.replace(/\/$/, "");
    }

    function validateResourcePath(value) {
      var path = requiredText(value, "CONFIG_RESOURCE_PATH_MISSING");
      if (
        path.charAt(0) !== "/" ||
        path.indexOf("://") >= 0 ||
        path.indexOf("..") >= 0 ||
        /[\r\n]/.test(path)
      ) {
        throw integrationError("CONFIG_RESOURCE_PATH_INVALID", 0, false, "");
      }
      return path;
    }

    function validateCorrelationId(value) {
      var correlationId = requiredText(value, "CONFIG_CORRELATION_ID_MISSING");
      if (
        !/^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$/.test(
          correlationId
        )
      ) {
        throw integrationError("CONFIG_CORRELATION_ID_INVALID", 0, false, "");
      }
      return correlationId;
    }

    function parseJson(content, category, httpStatus) {
      try {
        return Platform.Function.ParseJSON(String(content));
      } catch (ignored) {
        throw integrationError(category, httpStatus, false, "");
      }
    }

    function getHeader(headers, requestedName) {
      var key;
      if (!headers) {
        return "";
      }
      for (key in headers) {
        if (String(key).toLowerCase() === requestedName.toLowerCase()) {
          return String(headers[key]).substring(0, 64);
        }
      }
      return "";
    }

    function classifyHttpFailure(statusCode, headers, prefix) {
      var retryable =
        statusCode === 0 ||
        statusCode === 408 ||
        statusCode === 429 ||
        statusCode === 500 ||
        statusCode === 502 ||
        statusCode === 503 ||
        statusCode === 504;
      return integrationError(
        prefix + "_HTTP_" + statusCode,
        statusCode,
        retryable,
        retryable ? getHeader(headers, "Retry-After") : ""
      );
    }

    function sendJsonRequest(url, method, postData, authorization) {
      var request = new Script.Util.HttpRequest(url);
      request.method = method;
      request.contentType = "application/json";
      request.encoding = "UTF-8";
      request.retries = 0;
      request.continueOnError = true;
      request.setHeader("Accept", "application/json");
      if (authorization !== "") {
        request.setHeader("Authorization", authorization);
      }
      if (postData !== null) {
        request.postData = Platform.Function.Stringify(postData);
      }
      return request.send();
    }

    function acquireServerToServerToken(oauthConfig) {
      var tokenRequest = {
        grant_type: "client_credentials",
        client_id: requiredText(oauthConfig.clientId, "CONFIG_CLIENT_ID_MISSING"),
        client_secret: requiredText(oauthConfig.clientSecret, "CONFIG_CLIENT_SECRET_MISSING")
      };
      var tokenUrl = validateAuthorizationBaseUri(oauthConfig.authorizationBaseUri) + "v2/token";
      var response;
      var document;

      if (oauthConfig.targetMid !== null && oauthConfig.targetMid !== undefined) {
        if (
          isNaN(Number(oauthConfig.targetMid)) ||
          Number(oauthConfig.targetMid) < 1 ||
          Number(oauthConfig.targetMid) % 1 !== 0
        ) {
          throw integrationError("CONFIG_TARGET_MID_INVALID", 0, false, "");
        }
        tokenRequest.account_id = Number(oauthConfig.targetMid);
      }
      if (oauthConfig.scope !== null && oauthConfig.scope !== undefined) {
        tokenRequest.scope = String(oauthConfig.scope);
      }

      response = sendJsonRequest(tokenUrl, "POST", tokenRequest, "");
      if (!response || response.statusCode < 200 || response.statusCode >= 300) {
        throw classifyHttpFailure(
          response ? Number(response.statusCode) : 0,
          response ? response.headers : null,
          "OAUTH"
        );
      }

      document = parseJson(response.content, "OAUTH_RESPONSE_INVALID", response.statusCode);
      if (
        !document ||
        requiredText(document.token_type, "OAUTH_TOKEN_TYPE_MISSING") !== "Bearer" ||
        requiredText(document.access_token, "OAUTH_ACCESS_TOKEN_MISSING").length > 512 ||
        Number(document.expires_in) <= 0
      ) {
        throw integrationError("OAUTH_RESPONSE_INVALID", response.statusCode, false, "");
      }

      return {
        accessToken: document.access_token,
        expiresIn: Number(document.expires_in),
        restInstanceUrl: validateRestInstanceUrl(document.rest_instance_url)
      };
    }

    function writeIntegrationLog(logDE, event) {
      var inserted = Platform.Function.InsertData(
        requiredText(logDE, "CONFIG_LOG_DE_MISSING"),
        [
          "CorrelationId",
          "IntegrationCode",
          "Operation",
          "AttemptNumber",
          "Outcome",
          "ErrorCategory",
          "HttpStatus",
          "Retryable",
          "RetryAfter",
          "OccurredAt"
        ],
        [
          event.correlationId,
          event.integrationCode,
          event.operation,
          event.attemptNumber,
          event.outcome,
          event.errorCategory,
          event.httpStatus,
          event.retryable,
          event.retryAfter,
          Platform.Function.Now()
        ]
      );
      if (inserted !== 1) {
        throw integrationError("OPERATIONAL_LOG_WRITE_FAILED", 0, false, "");
      }
    }

    function runRestGet(oauthConfig, operationConfig, responseValidator) {
      var correlationId = validateCorrelationId(operationConfig.correlationId);
      var integrationCode = requiredText(
        operationConfig.integrationCode,
        "CONFIG_INTEGRATION_CODE_MISSING"
      );
      var operation = "REST_GET";
      var attemptNumber = Number(operationConfig.attemptNumber);
      var error;
      var token;
      var response;
      var document;

      if (attemptNumber < 1 || typeof responseValidator !== "function") {
        throw integrationError("CONFIG_OPERATION_INVALID", 0, false, "");
      }

      try {
        token = acquireServerToServerToken(oauthConfig);
        response = sendJsonRequest(
          token.restInstanceUrl + validateResourcePath(operationConfig.resourcePath),
          "GET",
          null,
          "Bearer " + token.accessToken
        );

        if (!response || response.statusCode < 200 || response.statusCode >= 300) {
          throw classifyHttpFailure(
            response ? Number(response.statusCode) : 0,
            response ? response.headers : null,
            "REST"
          );
        }

        document = parseJson(response.content, "REST_RESPONSE_INVALID", response.statusCode);
        if (!responseValidator(document)) {
          throw integrationError("REST_RESPONSE_SCHEMA_INVALID", response.statusCode, false, "");
        }

        writeIntegrationLog(operationConfig.logDE, {
          correlationId: correlationId,
          integrationCode: integrationCode,
          operation: operation,
          attemptNumber: attemptNumber,
          outcome: "Succeeded",
          errorCategory: "",
          httpStatus: response.statusCode,
          retryable: false,
          retryAfter: ""
        });
        return document;
      } catch (caught) {
        error = caught && caught.category
          ? caught
          : integrationError("UNEXPECTED_FAILURE", 0, false, "");

        try {
          writeIntegrationLog(operationConfig.logDE, {
            correlationId: correlationId,
            integrationCode: integrationCode,
            operation: operation,
            attemptNumber: attemptNumber,
            outcome: "Failed",
            errorCategory: error.category,
            httpStatus: error.httpStatus,
            retryable: error.retryable,
            retryAfter: error.retryAfter
          });
        } catch (loggingFailure) {
          throw new Error("OPERATIONAL_LOG_WRITE_FAILED; correlationId=" + correlationId);
        }
        throw new Error(error.category + "; correlationId=" + correlationId);
      }
    }
    </script>

The client-credentials grant has no refresh token. Reuse the returned access token within the run; an external service MAY cache it only in protected server-side storage and MUST expire it using `expires_in`. The retry owner MUST schedule, rather than busy-wait inside SSJS, no more than the approved maximum attempts. Retry only logged transient failures, honor `Retry-After` when supplied, and preserve the correlation ID and idempotency key across attempts. The example operation is GET; writes require an operation-specific idempotency contract before retries are enabled.

## Bounded WSProxy pagination

**Context:** Non-send SSJS context.  
**Inputs:** Approved object type, explicit property list, optional filter, and positive `maxPages` and `maxRows` limits.  
**Outcome:** Retrieves every permitted page with `getNextBatch`, or fails before exceeding the configured work boundary.

    <script runat="server">
    Platform.Load("Core", "1.1");

    function retrieveAllWsProxy(proxy, objectType, properties, filter, maxPages, maxRows) {
      var response;
      var rows = [];
      var pageCount = 0;
      var pageRows;
      var i;

      maxPages = Number(maxPages);
      maxRows = Number(maxRows);

      if (
        !proxy ||
        !objectType ||
        !properties ||
        properties.length === 0 ||
        isNaN(maxPages) ||
        maxPages % 1 !== 0 ||
        maxPages < 1 ||
        isNaN(maxRows) ||
        maxRows % 1 !== 0 ||
        maxRows < 1
      ) {
        throw new Error("WSPROXY_RETRIEVE_CONFIG_INVALID");
      }

      response = proxy.retrieve(objectType, properties, filter);

      while (true) {
        pageCount++;
        if (!response || response.Status === "Error") {
          throw new Error("WSPROXY_RETRIEVE_FAILED");
        }

        pageRows = response.Results || [];
        if (rows.length + pageRows.length > maxRows) {
          throw new Error("WSPROXY_ROW_LIMIT_EXCEEDED");
        }
        for (i = 0; i < pageRows.length; i++) {
          rows.push(pageRows[i]);
        }

        if (!response.HasMoreRows) {
          return {
            rows: rows,
            pageCount: pageCount,
            lastRequestId: response.RequestID || ""
          };
        }
        if (pageCount >= maxPages) {
          throw new Error("WSPROXY_PAGE_LIMIT_EXCEEDED");
        }
        if (!response.RequestID) {
          throw new Error("WSPROXY_CONTINUATION_ID_MISSING");
        }

        response = proxy.getNextBatch(objectType, response.RequestID);
      }
    }
    </script>

The caller MUST log the correlation ID, object type, page count, row count, final request ID, outcome, and safe error category. Do not log retrieved payloads by default. If a run reaches a work limit, persist an approved checkpoint outside the access-token store and continue through an orchestrated follow-up run only when the object and retrieval semantics support restart.

## SOAP operation decision

**Context:** External system integration.  
**Decision:** Use REST first. Use direct SOAP only when the required operation is unavailable through REST and cannot be performed through WSProxy in an approved non-send SSJS context.

For SOAP batch calls, inspect both overall and object-level status. Page retrieves, provide correlation, and reconcile partially successful writes.

## Related Topics

- [REST API](../../04_Integrations/REST_API.md)
- [SOAP and WSProxy](../../04_Integrations/SOAP_AND_WSPROXY.md)
- [Integration Reliability](../../04_Integrations/INTEGRATION_RELIABILITY.md)
- [Security, Performance, and Operations](../../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)

## Official References

- [Server-to-Server Integrations with Client Credentials Grant Type](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/integration-s2s-client-credentials)
- [Use SSJS via WSProxy with SOAP API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_useSSJS)

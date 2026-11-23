# SSJS and WSProxy Snippets

Use these fragments with the [required use checks](README.md#required-use-checks) and [SSJS reference](../../deep_dives/ssjs.md). Unless stated otherwise, they omit complete configuration, authorization, error handling, logging, monitoring, recovery, and release controls.

## Load the Core library

**Context:** CloudPages, landing page, microsite, or other approved non-message context.

    Platform.Load("Core", "1.1");

## Safe form-value presence check

**Context:** CloudPages request.

    var emailAddress = Platform.Request.GetFormField("emailAddress");
    if (!emailAddress || emailAddress.length > 254) {
      throw new Error("VALIDATION_EMAIL_ADDRESS");
    }

Apply request validation, authorization, and error handling through [Secure Data Capture](../../06_Solution_Patterns/SECURE_DATA_CAPTURE.md).

## Bounded WSProxy retrieve

**Context:** Non-send SSJS context.  
**Safe reuse:** Supply an approved object type, explicit property list, optional filter, and positive page and row limits. Log only the correlation ID, counts, request ID, outcome, and safe error category.

    function retrieveAllWsProxy(proxy, objectType, properties, filter, maxPages, maxRows) {
      var response = proxy.retrieve(objectType, properties, filter);
      var rows = [];
      var pageCount = 0;
      var pageRows;
      var i;

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

Validate `proxy`, `objectType`, `properties`, `maxPages`, and `maxRows` before invocation. Apply retrieval, continuation, Business Unit, and checkpoint requirements from [SOAP and WSProxy](../../04_Integrations/SOAP_AND_WSPROXY.md).

## Related Topics

- [SSJS reference](../../deep_dives/ssjs.md)
- [SOAP and WSProxy](../../04_Integrations/SOAP_AND_WSPROXY.md)

## Official References

- [Server-Side JavaScript](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_serverSideJavaScript.html)
- [Basic Retrieves via WSProxy](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_basic_retrieve)

# Server-Side JavaScript (SSJS)

## Platform Constraints

SSJS executes on Salesforce Marketing Cloud Engagement servers. It is not browser JavaScript and not Node.js. Do not assume access to a DOM, `window`, `document`, filesystem, sockets, npm, package managers, `require()`, `import`, `fetch()`, or unverified modern ECMAScript features.

Use only the documented Platform and Core libraries, `Script.Util` capabilities, and native JavaScript behavior verified for the Marketing Cloud Engagement runtime. Load the Core library explicitly when it is required. [Platform facts](../SFMC_PLATFORM_FACTS.md) take precedence over this reference.

## Execution Context

SSJS runs when supported content is rendered or when a Script Activity executes. The design must name one of these contexts:

- **CloudPages, landing pages, and microsites:** Preferred context for Core-library work and server-side application logic.
- **Automation Studio Script Activity:** Background procedural processing; design for timeouts, logging, replay, and recovery.
- **Email and mobile messages:** Use AMPscript or Platform functions for message work. Do not use Core-library functions in messages.
- **Non-send SSJS contexts:** WSProxy is available for supported SOAP-backed object operations.

SSJS activities time out after 30 minutes. A script that consistently approaches 10 minutes requires redesign, decomposition, or a more suitable processing platform.

## Known Limitations

- No browser APIs, external libraries, package installation, or general filesystem access.
- Runtime behavior is not equivalent to current browser or Node.js JavaScript.
- Core-library functions are intended for landing pages and applications, not email messages.
- WSProxy is unavailable in send contexts and is subject to SOAP API restrictions.
- HTTP requests using `Script.Util.HttpRequest` are limited to HTTP port 80 and HTTPS port 443; a `send()` call times out after 30 seconds.
- SSJS is not a substitute for set-based SQL transformations or a durable enterprise integration service.
- In-memory state is limited to the current execution; do not depend on it across requests or activity runs.

## Common Mistakes

- Treating SSJS as Node.js or browser JavaScript.
- Using Core-library functions in emails.
- Using SSJS for simple inline personalization that AMPscript can handle more directly.
- Making unbounded loops, repeated Data Extension calls, or sequential HTTP calls in a request path.
- Using raw SOAP when WSProxy supports the required operation.
- Assuming an HTTP retry is safe without an idempotency strategy.
- Hardcoding MIDs, Business Unit IDs, endpoints, credentials, folder IDs, or Data Extension identifiers.
- Returning raw exceptions or sensitive diagnostics to a CloudPages visitor.

## Purpose

Use SSJS for supported server-side procedural logic that cannot be expressed cleanly with AMPscript or SQL. Typical applications include CloudPages request handling, controlled Data Extension operations, Automation Studio processing, supported API calls, and SOAP-backed object operations through WSProxy.

Use AMPscript for simple, bounded personalization. Use SQL Query Activities for bulk transformation. Use REST-based or external integration services for large, long-running, or cross-system workloads that exceed the safe operational boundary of SSJS.

## Architecture

Structure SSJS as small, context-specific stages:

1. Load only the library required by the selected approach.
2. Read configuration and identify the execution context.
3. Validate and authorize inputs before processing.
4. Perform a bounded business operation.
5. Persist only idempotent, schema-valid changes.
6. Handle expected failures with controlled error paths.
7. Log safe operational details and return an appropriate response.

For CloudPages, keep presentation separate from server logic. For automations, use checkpoints, staging Data Extensions, quarantine outputs, and run-level correlation. For integrations, use an API-client envelope with authentication, timeout, status handling, pagination, retry ownership, and reconciliation.

## Best Practices

- Prefer AMPscript for simple personalization and SSJS only for context-appropriate procedural requirements.
- Declare the execution context, data contract, owner, and failure behavior for each script.
- Use `Platform.Load("Core", "1.1")` only when a Core-library function is needed.
- Prefer Platform functions where they meet the requirement; they are outside the Core library and are documented as faster for supported uses.
- Use WSProxy for supported in-platform SOAP object operations; it avoids legacy SSJS/Core translation overhead.
- Validate type, length, format, required values, and allowed values before a data mutation or outbound request.
- Use `try`/`catch` around operations that can fail, then record a safe error category and correlation identifier.
- Use batch operations and pagination for API or WSProxy work; never assume a single result page is complete.
- Make writes idempotent and document the key that prevents duplicate effects.
- Keep credentials in approved protected configuration and minimize package permissions.
- Use explicit, descriptive `lowerCamelCase` variable names and `UPPER_SNAKE_CASE` configuration constants.

## Anti-patterns

| Anti-pattern | Why it fails | Preferred approach |
|---|---|---|
| `fetch()` or npm dependency | Not a supported SSJS runtime assumption. | Use documented Platform, Core, or `Script.Util` capabilities. |
| Core library in an email | Core usage is reserved for landing pages and applications. | Use AMPscript or supported Platform functions. |
| Row-by-row bulk transformation | Slow, difficult to recover, and hard to observe. | Use SQL Query Activities and Automation Studio. |
| Unbounded outbound calls | Risks request limits, timeout, and duplicate side effects. | Batch, paginate, set a timeout, and define idempotency. |
| Direct visitor error output | Leaks internal implementation details. | Log internally; return a safe response. |
| Raw SOAP from SSJS by default | Adds complexity and may add overhead. | Use WSProxy when it supports the object operation. |
| Long-running Script Activity | Risks timeout and fragile recovery. | Decompose into staged jobs or use an external processing service. |

## Performance

- Prefer SQL for set-based data work and Automation Studio for scheduling.
- Minimize Data Extension calls, nested loops, payload size, and sequential network requests.
- Use WSProxy batch methods where supported and paginate retrievals using the returned pagination state.
- Keep CloudPages request processing bounded; defer heavy work to an asynchronous process.
- For `Script.Util.HttpRequest`, set only necessary headers, use the appropriate HTTP method, and design around its 30-second request timeout.
- Limit and classify retries. Retry only transient failures that are safe to replay.
- Measure duration, records processed, API calls, error count, retry count, and backlog per run.
- Stop optimizing only after compatibility, security, and correctness are established.

## Security

- Treat form fields, query parameters, request bodies, API responses, and Data Extension values as untrusted until validated for their intended use.
- Authenticate and authorize before performing state-changing CloudPages actions.
- Keep secrets out of source code, client-visible HTML, URLs, logs, and diagnostic output.
- Use least-privilege Installed Packages and explicitly review Business Unit (BU) access.
- Do not concatenate untrusted input into a query, URL, redirect target, or JSON body without strict validation and appropriate encoding.
- Use allowlists for redirect destinations, operation names, and externally supplied identifiers.
- Mask or omit personal data and credentials from logs.
- Fail closed when identity, authorization, consent, or request integrity cannot be established.
- Treat cross-BU WSProxy context switching as a privileged operation requiring explicit ownership and auditability.

## Debugging

1. Confirm the execution context and whether Platform, Core, or WSProxy functionality is permitted there.
2. Verify library loading, function names, input types, and Data Extension schema assumptions.
3. Add a correlation identifier and capture safe diagnostics at each processing stage.
4. Test malformed inputs, missing records, duplicate submissions, authorization failures, and dependency timeouts.
5. Inspect HTTP status, response body, and retry behavior without logging secrets.
6. For WSProxy retrieves, check `HasMoreRows` and paginate before concluding a result set is complete.
7. For writes, distinguish validation failures, duplicate-key behavior, transient dependency failures, and permanent platform errors.
8. Reproduce with the smallest safe payload; do not expose temporary debugging output in production pages or messages.

## Examples

### Guarded CloudPages form capture

**Context:** CloudPages POST request.  
**Inputs:** `emailAddress` form field; a Data Extension with a primary key and retention policy.  
**Behavior:** Validates a minimal input contract, performs a controlled insert, and returns a safe result.

```javascript
<script runat="server">
Platform.Load("Core", "1.1");

var correlationId = Platform.Function.GUID();
var emailAddress = Platform.Request.GetFormField("emailAddress");

try {
  if (!emailAddress || emailAddress.length > 254) {
    throw new Error("VALIDATION_EMAIL_ADDRESS");
  }

  var insertedCount = Platform.Function.InsertData(
    "FormSubmissionLog",
    ["CorrelationId", "EmailAddress"],
    [correlationId, emailAddress]
  );

  Platform.Response.Write(insertedCount === 1 ? "Request received." : "Unable to process request.");
} catch (error) {
  /* Log correlationId and a safe error category through the approved logging path. */
  Platform.Response.Write("Unable to process request.");
}
</script>
```

This example is intentionally incomplete for production: add authorization where the action is identity-bound, full email validation, output encoding, consent handling, an idempotency key, and approved structured logging. Do not expose `error.message` to visitors.

### Bounded HTTP request envelope

**Context:** CloudPages or Automation Studio Script Activity.  
**Behavior:** Makes one documented outbound request and classifies a non-success response for the caller.

```javascript
<script runat="server">
var request = new Script.Util.HttpRequest("https://api.example.invalid/customer-status");
request.method = "GET";
request.contentType = "application/json";
request.retries = 0;
request.continueOnError = true;

var response = request.send();

if (response.statusCode < 200 || response.statusCode >= 300) {
  throw new Error("DEPENDENCY_HTTP_" + response.statusCode);
}
</script>
```

Production use requires approved authentication, endpoint configuration, response-schema validation, safe logging, idempotency where requests change state, and a bounded retry policy owned by the calling process.

### WSProxy paginated retrieve

**Context:** Non-send SSJS context.  
**Behavior:** Retrieves the first result page and makes pagination explicit.

```javascript
<script runat="server">
var proxy = new Script.Util.WSProxy();
var result = proxy.retrieve("DataExtension", ["Name", "CustomerKey"]);

if (result.HasMoreRows) {
  /* Continue with the documented WSProxy pagination method before treating results as complete. */
}
</script>
```

Specify the required properties, filters, account context, maximum work per run, and continuation strategy before using this pattern in production.

## Related Topics

- [Platform facts](../SFMC_PLATFORM_FACTS.md)
- [Architecture skill](../../SKILL.md)
- [Execution Context Catalog](../01_Foundations/EXECUTION_CONTEXT_CATALOG.md)
- [Technology Selection Guide](../01_Foundations/TECHNOLOGY_SELECTION_GUIDE.md)
- [Security, Performance, and Operations](../01_Foundations/SECURITY_PERFORMANCE_AND_OPERATIONS.md)
- [SOAP and WSProxy](../04_Integrations/SOAP_AND_WSPROXY.md)
- [REST API](../04_Integrations/REST_API.md)
- [Secure Data Capture](../06_Solution_Patterns/SECURE_DATA_CAPTURE.md)
- [SSJS Examples](../07_Reusable_Assets/examples/SSJS_EXAMPLES.md)
- [SSJS and WSProxy Snippets](../07_Reusable_Assets/snippets/SSJS_AND_WSPROXY_SNIPPETS.md)

## Official References

- [Server-Side JavaScript](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_serverSideJavaScript.html)
- [SSJS Syntax Guide](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_syntaxGuide)
- [Platform Functions](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformFunctions)
- [Script.Util.HttpRequest](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformContentSyndicationScriptUtilHttpRequest.html)
- [InsertData](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformDataExtensionInsertData)
- [GUID](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_platformUtilityGUID.html)
- [Use SSJS via WSProxy with SOAP API](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_useSSJS)
- [Basic Retrieves via WSProxy](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_basic_retrieve)
- [Create via WSProxy](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_create.html)

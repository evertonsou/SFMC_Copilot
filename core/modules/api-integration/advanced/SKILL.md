---
name: sfmc-api-advanced
description: Advanced API patterns - Bulk operations, async processing, rate limiting.
---

# API - Advanced Patterns

## Rate Limiting

| Package Type | Requests/minute |
|--------------|-----------------|
| Basic | 10,000 |
| Pro | 25,000 |
| Enterprise | 100,000+ |

## Retry with Exponential Backoff

```javascript
<script runat="server">
Platform.Load("core", "1");

function makeAPICallWithRetry(url, options, maxRetries) {
  var retries = 0;
  var delay = 1000;
  
  while (retries < maxRetries) {
    var response = HTTP.Get(url, options);
    var statusCode = response.StatusCode;
    
    if (statusCode >= 200 && statusCode < 300) {
      return response;
    }
    
    if (statusCode == 429 || statusCode >= 500) {
      // Rate limited or server error - wait and retry
      retries++;
      if (retries < maxRetries) {
        Platform.Sleep(delay);
        delay *= 2; // Exponential backoff
      }
    } else {
      // Client error - don't retry
      return null;
    }
  }
  
  return null; // Failed after retries
}
</script>
```

## Async Operations

### Create Async Job
```javascript
POST /data/v1/async/dataextensions/key:{key}/rows
{
  "items": [...]
}
```
**Response:**
```json
{
  "requestId": "abc123",
  "status": "Pending"
}
```

### Check Job Status
```javascript
GET /data/v1/async/jobs/{requestId}
```
**Response:**
```json
{
  "requestId": "abc123",
  "status": "Processing",
  "progress": "50"
}
```

### Poll for Completion
```javascript
<script runat="server">
var status = "Pending";
var requestId = "abc123";

while (status === "Pending" || status === "Processing") {
  Platform.Sleep(2000); // Wait 2 seconds
  
  var statusResponse = HTTP.Get(
    "https://YOUR_SUBDOMAIN.rest.marketingcloudapis.com/data/v1/async/jobs/" + requestId,
    ["Authorization: Bearer " + accessToken]
  );
  
  var result = Platform.ParseJSON(statusResponse.Content);
  status = result.status;
  
  if (status === "Failed") {
    // Handle error
  }
}
</script>
```

## Bulk Extract

```javascript
<script runat="server">
// Create bulk extract activity
var result = api.perform("DataExtension", "startBulkExtract", {
  ID: 12345,
  OutputMode: "华山"
});

// Poll for completion and get file
</script>
```

## Request Batching

```javascript
<script runat="server">
// Instead of multiple calls
for (var i = 0; i < 100; i++) {
  api.createItem("DataExtension", { ... }); // 100 API calls
}

// Use batch (when available)
var objects = [];
for (var i = 0; i < 100; i++) {
  objects.push({ Name: "Item" + i, CustomerKey: "key" + i });
}
api.createBatch("DataExtension", objects); // 1 API call
</script>
```

## Caching

```javascript
<script runat="server">
// Cache for 5 minutes
Platform.Cache.SetValue("api_data", JSON.stringify(data), 300);
Platform.Cache.SetValue("cache_expiry", Date.now() + 300000, 300);
</script>
```
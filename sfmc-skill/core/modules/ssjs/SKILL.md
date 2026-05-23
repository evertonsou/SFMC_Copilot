---
name: sfmc-ssjs
description: Server-Side JavaScript for Salesforce Marketing Cloud. Platform and Core libraries, WSProxy, and patterns.
---

# SFMC SSJS Module

Server-Side JavaScript para lógica avançada em CloudPages e automations.

## Características

| Feature | Support |
|---------|---------|
| Arrays | ✅ |
| Objects | ✅ |
| JSON parsing | ✅ |
| Try/catch | ✅ |
| ES6 features | Partial |
| External HTTP | ✅ |
| Date | ✅ |

## Platform vs Core

```javascript
// Platform - SFMC specific functions
Platform.Load("core", "1");
Platform.Cache.SetValue(key, value);
Platform.Request.GetFormField("name");

// Core - Standard JS extensions
var arr = [1, 2, 3];
arr.forEach(function(item) { });
Platform.Load("core", "1");
```

## Platform Library

```javascript
Platform.Load("core", "1");

// Request
Platform.Request.GetFormField("email");
Platform.Request.GetQueryStringParameter("param");
Platform.Request.Method; // "GET" or "POST"
Platform.Request.ClientIP;

// Response
Platform.Response.AddHeader("Content-Type", "application/json");
Platform.Response.Redirect("url");
Platform.Response.SetStatus(404);
Platform.Response.End();

// Cache
Platform.Cache.SetValue(key, value, ttlSeconds);
Platform.Cache.GetValue(key);
Platform.Cache.RemoveValue(key);

// GUID
var guid = Platform.Guid("NewGuid");

// Date
var now = new Date();

// Sleep (milliseconds)
Platform.Sleep(1000);
```

## DataExtension Operations

```javascript
<script runat="server">
Platform.Load("core", "1");

// Initialize DE
var de = DataExtension.Init("CustomerKey");

// Add Row
de.Rows.Add({
  Email: "test@example.com",
  Name: "Test"
});

// Update Rows
de.Rows.Update({
  Status: "Active"
}, ["Email", "test@example.com"]);

// Remove Rows
de.Rows.Remove(["Email", "test@example.com"]);

// Get Row Count
var count = de.Rows.Retrieve().length;
</script>
```

## WSProxy (Advanced)

```javascript
<script runat="server">
Platform.Load("core", "1");
var api = new Script.Util.WSProxy();

// Retrieve
var result = api.retrieve("DataExtension", ["Name", "CustomerKey"], {
  Property: "Status",
  SimpleOperator: "equals",
  Value: "Active"
});

// Create
api.createItem("DataExtension", {
  Name: "NewDE",
  CustomerKey: "new-de-key",
  Fields: [{ Name: "Email", Type: "EmailAddress" }]
});
</script>
```

## HTTP Operations

```javascript
<script runat="server">
Platform.Load("core", "1");

// GET
var response = HTTP.Get("https://api.example.com/data");

// POST
var payload = '{"email":"test@example.com"}';
var response = HTTP.Post(url, "application/json", payload);

// Headers
var response = HTTP.GetWithHeaders(url, ["Authorization: Bearer token"]);
</script>
```

## Error Handling

```javascript
<script runat="server">
Platform.Load("core", "1");

try {
  var de = DataExtension.Init("NonExistent");
  var rows = de.Rows.Retrieve();
} catch (e) {
  Write("Error: " + e.message);
  // Log or handle error
}
</script>
```

## JSON Operations

```javascript
<script runat="server">
Platform.Load("core", "1");

// Parse
var obj = Platform.ParseJSON('{"email":"test@example.com"}');

// Stringify
var str = Stringify(obj);

// Access
var email = obj.email;
</script>
```

## Array/Object Operations

```javascript
<script runat="server">
Platform.Load("core", "1");

// Arrays
var arr = [1, 2, 3];
arr.push(4);
arr.forEach(function(item) { 
  Write(item); 
});

// Filter
var filtered = arr.filter(function(item) {
  return item > 2;
});

// Map
var doubled = arr.map(function(item) {
  return item * 2;
});

// Objects
var obj = { name: "Test", email: "test@example.com" };
obj.name = "Updated";
</script>
```

## Patterns

### Form Handler Pattern
```javascript
<script runat="server">
Platform.Load("core", "1");

if (request.method == "POST") {
  var email = Platform.Request.GetFormField("email");
  
  if (email && email.includes("@")) {
    var de = DataExtension.Init("Leads");
    de.Rows.Add({ Email: email, CreatedDate: new Date() });
    Platform.Response.Redirect("thank-you");
  }
}
</script>
```
---
name: sfmc-api-wsproxy
description: WSProxy advanced usage for complex operations in SFMC.
---

# API - WSProxy

WSProxy permite operações API completas via SSJS.

## Setup Básico

```javascript
<script runat="server">
Platform.Load("core", "1");

var api = new Script.Util.WSProxy();
api.setClientId("YOUR_CLIENT_ID");
api.setClientSecret("YOUR_CLIENT_SECRET");
</script>
```

## Retrieve

```javascript
<script runat="server">
var cols = ["Name", "CustomerKey", "Status", "RowCount"];
var filter = {
  Property: "Status",
  SimpleOperator: "equals",
  Value: "Active"
};

var result = api.retrieve("DataExtension", cols, filter);

if (result.StatusCode === "OK") {
  var items = result.Results;
  for (var i = 0; i < items.length; i++) {
    Write(items[i].Name + "<br>");
  }
}
</script>
```

## Create

```javascript
<script runat="server">
var de = {
  Name: "NewDE",
  CustomerKey: "new-de-key",
  Fields: [
    { Name: "Email", Type: "EmailAddress", IsPrimaryKey: true, IsRequired: true },
    { Name: "Name", Type: "String", MaxLength: 100 }
  ]
};

var result = api.createItem("DataExtension", de);
Write(result.StatusCode);
</script>
```

## Update

```javascript
<script runat="server">
var de = {
  CustomerKey: "existing-de-key",
  Name: "Updated Name",
  Description: "New description"
};

var result = api.updateItem("DataExtension", de);
</script>
```

## Delete

```javascript
<script runat="server">
var result = api.deleteItem("DataExtension", { CustomerKey: "de-to-delete" });
</script>
```

## Describe (Get Schema)

```javascript
<script runat="server">
var result = api.describe("DataExtension");
var properties = result.Results[0].Properties.Property;
for (var i = 0; i < properties.length; i++) {
  Write(properties[i].Name + "<br>");
}
</script>
```

## Perform (Execute Actions)

```javascript
<script runat="server">
var result = api.perform("DataExtension", "startBulkExtract", {
  ID: 12345,
  OutputMode: "华山"
});
</script>
```

## Pagination

```javascript
<script runat="server">
var allRows = [];
var cols = ["Name", "CustomerKey"];
var filter = { Property: "Status", SimpleOperator: "equals", Value: "Active" };
var queryAll = false;
var page = 1;
var pageSize = 2500;

while (!queryAll) {
  var result = api.retrieve("DataExtension", cols, filter, {
    BatchSize: pageSize,
    CurrentPage: page
  });
  
  if (result.Results) {
    allRows = allRows.concat(result.Results);
  }
  
  if (result.Page == result.TotalPages) {
    queryAll = true;
  } else {
    page++;
  }
}
</script>
```

## Batch Operations

```javascript
<script runat="server">
var objects = [
  { Name: "DE1", CustomerKey: "de1-key" },
  { Name: "DE2", CustomerKey: "de2-key" },
  { Name: "DE3", CustomerKey: "de3-key" }
];

var result = api.createBatch("DataExtension", objects);
// Creates all in one API call
</script>
```
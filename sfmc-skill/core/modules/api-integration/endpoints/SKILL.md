---
name: sfmc-api-rest
description: REST API endpoints and patterns for Salesforce Marketing Cloud.
---

# API - REST Endpoints

## Base URLs

```
https://YOUR_SUBDOMAIN.rest.marketingcloudapis.com
https://YOUR_SUBDOMAIN.soap.marketingcloudapis.com (SOAP)
```

## Endpoints Principais

| Recurso | Endpoint | Métodos |
|---------|----------|---------|
| Assets | `/asset/v1/content/assets` | GET, POST |
| Data Extensions | `/data/v1/` | GET, POST, PUT, DELETE |
| Contacts | `/contacts/v1/` | GET, POST |
| Journeys | `/interaction/v1/` | GET, POST |
| Automations | `/automation/v1/` | GET, POST |
| Campaigns | `/campaign/v1/` | GET, POST |
| Tracking | `/tracking/v1/` | GET |

## Data Extension Rows

### Get Rows
```javascript
GET /data/v1/async/dataextensions/key:{externalKey}/rows
```

### Insert Rows
```javascript
POST /data/v1/async/dataextensions/key:{externalKey}/rows
{
  "items": [
    { "Email": "test@example.com", "Name": "Test" }
  ]
}
```

### Update Rows
```javascript
PUT /data/v1/async/dataextensions/key:{externalKey}/rows
{
  "items": [
    { "Email": "test@example.com", "Name": "Updated" }
  ]
}
```

## Assets

### Create Asset
```javascript
POST /asset/v1/content/assets
{
  "name": "My Email",
  "assetType": { "name": "htmlemail", "id": 207 },
  "views": {
    "html": { "content": "<html>...</html>" }
  }
}
```

## Journeys

### Create Journey
```javascript
POST /interaction/v1/interactions
{
  "name": "My Journey",
  "key": "my-journey-key",
  "entrySource": {
    "objectTypeId": "test",
    "name": "API Entry"
  },
  "status": "Draft"
}
```

## Triggered Send

### Create Triggered Send Definition
```javascript
POST /messaging/v1/async/definitions
{
  "name": "Triggered Send",
  "customerKey": "triggered-send-key",
  "channel": "EMAIL",
  "subscriptionDefinition": {
    "criteria": "EmailAddress IS NOT NULL"
  }
}
```

### Send Triggered Email
```javascript
POST /messaging/v1/messageDefinitionSends/key:{definitionKey}/send
{
  "To": {
    "Address": "test@example.com",
    "SubscriberKey": "test@example.com",
    "ContactAttributes": {
      "SubscriberAttributes": {
        "FirstName": "John"
      }
    }
  }
}
```

## REST API via SSJS

```javascript
<script runat="server">
Platform.Load("core", "1");

// Get token (simplified)
var accessToken = "YOUR_ACCESS_TOKEN";
var restBase = "https://YOUR_SUBDOMAIN.rest.marketingcloudapis.com";

// Get Data Extension rows
var url = restBase + "/data/v1/async/dataextensions/key:YOUR_DE_KEY/rows";
var response = HTTP.Get(url, [
  "Authorization: Bearer " + accessToken,
  "Content-Type: application/json"
]);

var data = Platform.ParseJSON(response.Content);
</script>
```
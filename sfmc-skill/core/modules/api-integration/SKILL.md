---
name: sfmc-api-integration
description: Salesforce Marketing Cloud API integration - REST, SOAP, WSProxy, authentication, and advanced patterns.
---

# SFMC API Integration Module

Referência completa para APIs do Salesforce Marketing Cloud.

**Referência Principal**: https://developer.salesforce.com/developer-centers/marketing-cloud

## Visão Geral

| API | Uso Principal |
|-----|--------------|
| REST API | Operações CRUD, triggers, jornadas |
| SOAP API | Operações complexas, metadata |
| WSProxy | Scripting (SSJS) com API completa |
| Bulk API | Grandes volumes de dados |

## Carregamento de Sub-módulos

- `api/authentication` - OAuth 2.0, JWT, Client Credentials
- `api/rest` - REST API endpoints e patterns
- `api/soap` - SOAP API envelope e operations
- `api/wsproxy` - WSProxy avançado
- `api/endpoints` - Todos os endpoints
- `api/advanced` - Bulk, async, rate limiting

## Autenticação Básica

```javascript
// REST API Authentication
var authEndpoint = "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v2/token";
var payload = {
  grant_type: "client_credentials",
  client_id: "YOUR_CLIENT_ID",
  client_secret: "YOUR_CLIENT_SECRET"
};

var authResponse = HTTP.Post(authEndpoint, "application/json", Stringify(payload));
var token = Platform.ParseJSON(authResponse.Content).access_token;
```

## WSProxy Básico

```javascript
<script runat="server">
Platform.Load("core", "1");

var api = new Script.Util.WSProxy();
api.setClientId("YOUR_CLIENT_ID");
api.setClientSecret("YOUR_CLIENT_SECRET");

// Retrieve
var cols = ["Name", "CustomerKey", "Status"];
var filter = {
  Property: "CustomerKey",
  SimpleOperator: "equals",
  Value: "my-de-key"
};
var result = api.retrieve("DataExtension", cols, filter);

Write(Stringify(result));
</script>
```

## REST API Call

```javascript
<script runat="server">
Platform.Load("core", "1");

// Get token
var auth = HTTP.Post(
  "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v2/token",
  "application/json",
  '{"grant_type":"client_credentials","client_id":"ID","client_secret":"SECRET"}'
);
var token = Platform.ParseJSON(auth.Content).access_token;

// API Call
var headers = [
  "Authorization: Bearer " + token,
  "Content-Type: application/json"
];

var response = HTTP.Get(
  "https://YOUR_SUBDOMAIN.rest.marketingcloudapis.com/data/v1/async/dataextensions/key:DE_KEY/rows",
  headers
);
</script>
```
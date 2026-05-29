---
name: sfmc-api-authentication
description: SFMC API authentication methods - OAuth 2.0, JWT, Client Credentials, and more.
---

# API - Authentication

## Authentication Methods

| Method | Use Case | Token Lifetime |
|--------|----------|---------------|
| Client Credentials | Server-to-server | 60 min |
| JWT Bearer Token | Server-to-server | 60 min |
| OAuth Web Server | User authorization | 60 min |
| Installed App | Desktop/mobile apps | 60 min |

## Client Credentials (Recommended for Server-to-Server)

```javascript
<script runat="server">
Platform.Load("core", "1");

var authEndpoint = "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v2/token";
var payload = {
  grant_type: "client_credentials",
  client_id: "YOUR_CLIENT_ID",
  client_secret: "YOUR_CLIENT_SECRET"
};

var authResponse = HTTP.Post(authEndpoint, "application/json", Stringify(payload));
var accessToken = Platform.ParseJSON(authResponse.Content).access_token;
var restBase = Platform.ParseJSON(authResponse.Content).rest_instance_url;
</script>
```

## JWT Bearer Token

```javascript
<script runat="server">
// Generate JWT
var header = Base64Encode('{"alg":"RS256","typ":"JWT"}');
var claims = Base64Encode(Stringify({
  iss: "YOUR_CLIENT_ID",
  sub: "YOUR_USER_ID",
  aud: "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com",
  exp: Math.floor(Date.now() / 1000) + 3600
}));

// Sign with private key (requires crypto library)
// Then exchange for access token

var tokenEndpoint = "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/v2/token";
var payload = {
  grant_type: "urn:ietf:params:oauth:grant-type:jwt-bearer",
  assertion: signedJwt
};
</script>
```

## Token Refresh

```javascript
<script runat="server">
Platform.Load("core", "1");

// Check if token is still valid
var tokenExpiry = Platform.Cache.GetValue("sfmc_token_expiry");
var now = new Date().getTime();

if (!tokenExpiry || now >= tokenExpiry) {
  // Refresh token
  var auth = HTTP.Post(authEndpoint, "application/json", Stringify({
    grant_type: "client_credentials",
    client_id: "YOUR_CLIENT_ID",
    client_secret: "YOUR_CLIENT_SECRET"
  }));
  
  var result = Platform.ParseJSON(auth.Content);
  var accessToken = result.access_token;
  var restBase = result.rest_instance_url;
  
  // Cache with expiry (subtract 5 min buffer)
  var expiresIn = (result.expires_in - 300) * 1000;
  Platform.Cache.SetValue("sfmc_access_token", accessToken, expiresIn / 1000);
  Platform.Cache.SetValue("sfmc_token_expiry", now + expiresIn, expiresIn / 1000);
  Platform.Cache.SetValue("sfmc_rest_base", restBase, expiresIn / 1000);
}
</script>
```

## SOAP API Authentication

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
  <soapenv:Header>
    <ns1:Security xmlns:ns1="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
      <ns1:UsernameToken>
        <ns1:Username>USERNAME</ns1:Username>
        <ns1:Password>PASSWORD</ns1:Password>
      </ns1:UsernameToken>
    </ns1:Security>
  </soapenv:Header>
  <soapenv:Body>
    <!-- Request -->
  </soapenv:Body>
</soapenv:Envelope>
```

## Best Practices

1. **Cache tokens** - Evite autenticacao em cada request
2. **Token refresh** - Implemente refresh 5 min antes do expiry
3. **Credential storage** - Nunca hardcode secrets em arquivos
4. **Use HTTPS** - Sempre use conexoes seguras
5. **Rate limiting** - Respeite limites de API
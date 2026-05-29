---
name: sfmc-cloudpages-security
description: CloudPages security best practices - validation, sanitization, CSRF protection.
---

# CloudPages - Security

## Validação Server-Side

```javascript
<script runat="server">
Platform.Load("core", "1");

function validateEmail(email) {
  var regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}

function sanitizeInput(input) {
  if (typeof input !== "string") return input;
  return input.replace(/<[^>]*>/g, "").trim();
}

if (request.method == "POST") {
  var email = Platform.Request.GetFormField("email");
  var name = Platform.Request.GetFormField("name");
  
  // Validate
  if (!validateEmail(email)) {
    // Handle error
  }
  
  // Sanitize
  var cleanName = sanitizeInput(name);
  
  // Insert
  var de = DataExtension.Init("Leads");
  de.Rows.Add({ 
    Email: email, 
    Name: cleanName 
  });
}
</script>
```

## CSRF Protection

```javascript
<script runat="server">
// Generate token on page load
var csrfToken = Platform.Guid("NewGuid");
// Store in session or cookie

if (request.method == "POST") {
  var submittedToken = Platform.Request.GetFormField("csrf_token");
  // Compare with stored token
  if (submittedToken !== storedToken) {
    // Reject request
  }
}
</script>

<form method="POST">
  <input type="hidden" name="csrf_token" value="%%=v(@csrfToken)=%%">
  <!-- Other fields -->
</form>
```

## Rate Limiting

```javascript
<script runat="server">
Platform.Load("core", "1");

var ip = Platform.Request.ClientIP;
var key = "rate_limit_" + ip;
var cache = Platform.Cache.GetValue(key);

if (cache && cache.count >= 5) {
  // Block - too many requests
  Platform.Response.StatusCode = 429;
  Write("Too many requests. Please try again later.");
  Platform.Response.End();
}

// Increment
var count = (cache ? cache.count : 0) + 1;
Platform.Cache.SetValue(key, { count: count }, 60); // 60 seconds
</script>
```

## HTTPS

CloudPages sempre usa HTTPS. Não há configuração necessária.

## Best Practices

1. **Sempre valide no server-side** - Nunca confie apenas em validação client-side
2. **Sanitize inputs** - Remova HTML/scripts
3. **Use parameterized queries** - Previne SQL injection
4. **Implemente CSRF tokens** - Para formulários críticos
5. **Rate limiting** - Proteja contra spam/abuso
6. **Não logue dados sensíveis** - Senhas, cartão de crédito, etc.
7. **HTTPS obrigatório** - CloudPages já oferece
---
name: sfmc-cloudpages
description: CloudPages landing pages development - forms, data capture, security, templates.
---

# SFMC CloudPages Module

Landing pages e formulários no Salesforce Marketing Cloud.

## Visão Geral

| Tópico | Descrição |
|--------|-----------|
| CloudPages Architecture | Estrutura de páginas |
| Form Handling | Processamento de formulários |
| Data Capture | Gravação em Data Extensions |
| SSJS Form Handlers | Processamento server-side |
| AJAX Submissions | Envio assíncrono |
| Security | Validação, sanitização, CSRF |
| URL Parameters | Passagem de parâmetros |

## Carregamento de Sub-módulos

- `cloudpages/forms` - Form handling patterns
- `cloudpages/data-capture` - Data Extension integration
- `cloudpages/security` - Security best practices
- `cloudpages/templates` - CloudPages templates

## URL Structure

```
https://[subdomain].cloudpage.salesforce.com/[page-id]
https://[subdomain].cloudpage.salesforce.com/[folder]/[page-name]
```

## Form com AMPscript

```html
%%[
  /* Process POST */
  IF Request.Method == "POST" THEN
    SET @email = RequestParameter("email")
    SET @name = RequestParameter("name")
    
    IF NOT EMPTY(@email) THEN
      InsertData("Leads", "Email", @email, "Name", @name, "Source", "LandingPage")
      SET @success = "true"
    ENDIF
  ENDIF
]%%

<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Landing Page</title>
</head>
<body>
  %%[ IF @success == "true" THEN ]%%
    <p>Obrigado! Seus dados foram enviados.</p>
  %%[ ELSE ]%%
    <form method="POST">
      <input type="email" name="email" required placeholder="Email">
      <input type="text" name="name" required placeholder="Nome">
      <button type="submit">Enviar</button>
    </form>
  %%[ ENDIF ]%%
</body>
</html>
```

## Form com SSJS

```html
<script runat="server">
  Platform.Load("core", "1");
  
  if (request.method == "POST") {
    var email = Platform.Request.GetFormField("email");
    var name = Platform.Request.GetFormField("name");
    
    if (email && email.includes("@")) {
      var de = DataExtension.Init("Leads");
      de.Rows.Add({
        Email: email,
        Name: name,
        Source: "LandingPage",
        CreatedDate: new Date()
      });
      
      // Redirect on success
      Platform.Response.Redirect("thank-you-page");
    }
  }
</script>
```

## AJAX Form Submission

```html
<form id="leadForm">
  <input type="email" name="email" required>
  <input type="text" name="name" required>
  <button type="submit">Enviar</button>
</form>

<script>
document.getElementById("leadForm").addEventListener("submit", function(e) {
  e.preventDefault();
  
  var formData = new FormData(this);
  
  fetch(window.location.href, {
    method: "POST",
    body: formData,
    credentials: "same-origin"
  })
  .then(response => response.json())
  .then(data => {
    if (data.success) {
      document.getElementById("leadForm").innerHTML = "<p>Obrigado!</p>";
    }
  });
});
</script>

<script runat="server">
Platform.Load("core", "1");
if (request.method == "POST" && Platform.Request.GetQueryStringParameter("ajax") == "true") {
  Platform.Response.AddHeader("Content-Type", "application/json");
  
  var email = Platform.Request.GetFormField("email");
  
  if (email && email.includes("@")) {
    var de = DataExtension.Init("Leads");
    de.Rows.Add({ Email: email, CreatedDate: new Date() });
    Write('{"success": true}');
  } else {
    Write('{"success": false, "error": "Invalid email"}');
  }
  
  Platform.Response.End();
}
</script>
```
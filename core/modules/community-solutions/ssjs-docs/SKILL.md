# Module: ssjs-docs.xyz

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1K  
> **Site**: [ssjsdocs.xyz](https://ssjsdocs.xyz) | Mantido pela comunidade SFMC

---

## Visão Geral

**ssjs-docs.xyz** é uma documentação **verificada e detalhada** de Server-Side JavaScript para Salesforce Marketing Cloud. Preenche lacunas da documentação oficial com exemplos práticos e comportamentos reais documentados pela comunidade.

---

## Por que Usar ssjs-docs.xyz

| Problema | Solução |
|----------|---------|
| Documentação oficial incompleta | ssjs-docs.xyz documenta comportamentos não-óbvios |
| Exemplos genéricos | Exemplos específicos do contexto SFMC |
| Funções não documentadas | Funções descobertas pela comunidade |
| Comportamentos inesperados | Gotchas e workarounds documentados |

---

## Seções Principais

### Core Library

| Objeto | Métodos Documentados |
|--------|---------------------|
| `Platform` | `.Load()`, `.Request.*`, `.Response.*`, `.Function.*` |
| `Variable` | `.GetValue()`, `.SetValue()` |
| `Attribute` | `.GetValue()`, `.SetValue()` |
| `Script.Util` | `.HttpRequest`, `.WSProxy` |
| `DataExtension` | `.Init()`, `.Rows.*` |
| `Email` | `.Send()`, `.IsValid()` |

### Platform.Request

```javascript
// Documentado em ssjs-docs.xyz com todos os métodos:
Platform.Request.GetFormField("fieldName")       // POST form field
Platform.Request.GetQueryStringParameter("key")  // GET query param
Platform.Request.IsSSL()                         // Is HTTPS?
Platform.Request.ClientIP()                      // Client IP
Platform.Request.UserAgent                       // Browser/client
Platform.Request.ReferrerURL                     // Página anterior
```

### Platform.Response

```javascript
// Controle de resposta HTTP
Platform.Response.Redirect("https://url.com");
Platform.Response.SetResponseHeader("X-Custom", "value");
Platform.Response.SetContentType("application/json");
Platform.Response.SetStatus("404 Not Found");
```

### Platform.Function (Funções Documentadas)

```javascript
// Funções úteis documentadas no ssjs-docs.xyz
Platform.Function.ParseJSON(string)          // JSON.parse SFMC-safe
Platform.Function.Stringify(object)          // JSON.stringify
Platform.Function.Base64Encode(string)       // Base64
Platform.Function.Base64Decode(string)       // Base64 decode
Platform.Function.HMAC(key, msg, algo)       // HMAC-SHA256/SHA1
Platform.Function.MD5(string)               // MD5 hash
Platform.Function.GUID()                    // Gera UUID
Platform.Function.DateAdd(date, n, type)    // Adicionar tempo
Platform.Function.SystemDateToLocalDate(d)  // Converter timezone
Platform.Function.Length(value)             // Comprimento string/array
```

---

## Gotchas Documentados

### 1. `Write()` vs. `Variable.SetValue()`

```javascript
// Write() exibe imediatamente no output — não usar dentro de if() server
// Variable.SetValue() passa valor para AMPscript — usar para interoperabilidade

// ✅ Correto: trocar dados entre SSJS e AMPscript
Variable.SetValue("@resultado", "sucesso");

// ✅ Correto: output dinâmico
Write("<p>" + nome + "</p>");
```

### 2. `Platform.Function.ParseJSON` vs. `JSON.parse`

```javascript
// ❌ JSON.parse pode falhar em alguns contextos SFMC
var data = JSON.parse(responseContent);

// ✅ Usar sempre a versão SFMC
var data = Platform.Function.ParseJSON(responseContent);
```

### 3. Erros Silenciosos em DataExtension

```javascript
// DataExtension.Rows.Add não lança exceção em falha — sempre verificar retorno
var de = DataExtension.Init("DE_Nome");
var result = de.Rows.Add({ Campo: valor });

// result = 1 (sucesso) ou 0 (falha)
if (result !== 1) {
  // Logar erro ou lançar exceção manualmente
  throw new Error("Falha ao inserir na DE: " + Stringify(result));
}
```

### 4. Timeout em Script de CloudPage

```
Limite SFMC: 30 segundos por execução de script
Solução: Dividir processamento, usar Automation Studio para grandes volumes
```

### 5. `eval()` não é suportado

```javascript
// ❌ eval() não funciona em SFMC SSJS
eval("var x = 1;"); // Erro

// ✅ Alternativa: Platform.Function.ParseJSON para JSON dinâmico
```

---

## Funções da API Não-óbvias

```javascript
// HTTPRequest — documentação completa no ssjs-docs
var req = new Script.Util.HttpRequest("https://api.exemplo.com");
req.method = "POST";
req.postData = Stringify({ key: "value" });
req.setHeader("Content-Type", "application/json");
req.setHeader("Authorization", "Bearer " + token);
req.allowSelfSignedCert = false;  // HTTPS validação
req.emptyContentHandling = 0;     // 0=OK, 1=Erro se vazio
req.retries = 2;                  // Número de retentativas
req.continueOnError = true;       // Não lançar exceção em erro HTTP
var response = req.send();

// Propriedades do response
response.content        // Body como string
response.statusCode     // HTTP status code
response.contentType    // Content-Type header
```

---

## Referências Cruzadas

| Precisa de | Ir para |
|-----------|---------|
| Função específica | ssjs-docs.xyz/functions |
| Objeto Platform | ssjs-docs.xyz/platform |
| WSProxy | ssjs-docs.xyz/wsproxy |
| DataExtension | ssjs-docs.xyz/dataextension |
| Exemplos práticos | ssjs-lib ou sfmc-cookbook |
| Docs oficiais | developer.salesforce.com/mc |

---

## Links

- **Site**: https://ssjsdocs.xyz
- **GitHub do projeto**: https://github.com/ssjs-docs/ssjs-docs
- **Contribuir**: https://github.com/ssjs-docs/ssjs-docs/blob/main/CONTRIBUTING.md

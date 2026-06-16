---
name: sfmc-community-solutions
description: Community custom solutions and workarounds for non-native SFMC features.
---

# SFMC Community Solutions

Soluções customizadas da comunidade para recursos não-nativos do SFMC.

## Repositórios Principais

### SFMC-Cookbook
- **URL**: https://github.com/JoernBerkefeld/SFMC-Cookbook
- **Estrelas**: 136+
- **Autor**: Jörn Berkefeld (@JoernBerkefeld)
- **Descrição**: How to survive as a developer for Salesforce Marketing Cloud
- **Módulos**: FAQ, General, SSJS, AMPscript, Einstein Recommendations, Encryption
- **Use**: Guia completo de melhores práticas

#### SSJS Patterns (do SFMC-Cookbook)

##### Reading GET Parameters
```javascript
<script runat="server">
Platform.Load("core", "1.1.1");

var param = Platform.Request.GetQueryStringParameter("data");
</script>
```

##### Reading POST Data
```javascript
<script runat="server">
Platform.Load("core", "1.1.1");

var postBody = Platform.Request.GetPostData();
// Works only once! Save to variable if needed
</script>
```

##### AMPscript ↔ SSJS Variables
```javascript
// SSJS to AMPscript
Variable.SetValue('@myVar', temp);

// AMPscript to SSJS
var temp = Variable.GetValue('@myAmpscriptVariable');
```

##### Data Extension Operations
```javascript
// SELECT with Lookup
var myDe = DataExtension.Init(deName);
var myDeArr = myDe.Rows.Lookup(whereColumnArr, whereColumValueArr, limit, orderBy);

// INSERT
var count = Platform.Function.InsertData(deName, columnsArr, valuesArr);

// UPDATE
var count = Platform.Function.UpdateData(deName, whereCols, whereVals, setCols, setVals);

// UPSERT
var count = Platform.Function.UpsertData(deName, whereCols, whereVals, setCols, setVals);

// DELETE
var count = Platform.Function.DeleteData(deName, whereCols, whereVals);
```

##### SSJS Gotchas (SFMC-Cookbook)
```javascript
// ❌ FAILS - returning new object directly
function() {
    return { foo: bar };
}

// ✅ WORKS - assign to variable first
function() {
    var temp = { foo: bar };
    return temp;
}

// ❌ AVOID - "new" operator with return
function MyClass() {
    var service = { attr1: true };
    return service;
}
var myInstance = new MyClass(); // fails!

// ✅ USE - "new" with this.service
function MyClass() {
    this.service = { attr1: true };
}
var myInstance = new MyClass();
```

#### AMPscript Patterns (do SFMC-Cookbook)

##### Hide Code from Preview
```html
<div style="display:none">
%%[
// your code here
]%%
</div>
```

##### Dynamic Trackable Links
```html
%%[
SET @myParam = "bar"
SET @url = CONCAT("https://mydomain.com/path?foo=", @myParam)
]%%
<a href="%%=RedirectTo(@url)=%%">Link</a>
```

##### Data Extension Lookups
```ampscript
// Single value
Set @value = Lookup(@de, @returnField, @whereCol, @whereValue)

// Multiple rows (case-insensitive)
Set @rows = LookupRows(@de, @whereCol, @whereValue)

// Multiple rows sorted
Set @rows = LookupOrderedRows(@de, 2000, "MyField ASC", @whereCol, @whereValue)
```

#### ESLint Config for SSJS
```json
{
    "extends": ["eslint:recommended", "google"],
    "parserOptions": { "ecmaVersion": 3 },
    "globals": {
        "Platform": "readonly",
        "Variable": "readonly",
        "Attribute": "readonly",
        "DataExtension": "readonly",
        "Subscriber": "readonly",
        "Email": "readonly"
    }
}
```

### email360/ssjs-lib
- **URL**: https://github.com/email360/ssjs-lib
- **Estrelas**: 54
- **Descrição**: Library com JWT, logging, WSProxy wrappers
- **Use**: Production-ready SSJS utilities

### mc-cloud-pages-tricks
- **URL**: https://github.com/MarketingThibs/mc-cloud-pages-tricks
- **Descrição**: CloudPages debug tools e tricks
- **Use**: Troubleshooting CloudPages

### sfmc-data-views
- **URL**: https://github.com/MarketingThibs/sfmc-data-views
- **Descrição**: Clone queries para DataViews
- **Use**: Reporting queries

### qrcode-sfmc-ssjs
- **URL**: https://github.com/jp-ed/qrcode-sfmc-ssjs
- **Descrição**: QR Code Generator via SSJS
- **Use**: Gerar QR codes em emails

### shorten-sfmc-links-ampscript-sms
- **URL**: https://github.com/MarketingThibs/shorten-sfmc-links-ampscript-sms
- **Descrição**: Personalized Bitly links para SMS
- **Use**: Link tracking em SMS

### sfmc-data-views-sql-schema
- **URL**: https://github.com/MateuszDabrowski/sfmc-data-views-sql-schema
- **Descrição**: Complete SQL schema for DataViews
- **Use**: Query reference

## Referências da Comunidade

### Blogs Técnicos
| Blog | URL | Conteúdo |
|------|-----|----------|
| Mateusz Dabrowski | mateuszdabrowski.pl | SQL, SSJS, Config |
| sfmarketing.cloud | sfmarketing.cloud | API, CloudPages, AMPscript |

### Ferramentas
| Tool | URL | Uso |
|------|-----|-----|
| ampscript.io | ampscript.io | Test AMPscript |
| dataviews.io | dataviews.io | Visual DataViews reference |

## Exemplos de Uso

### JWT Generation (email360/ssjs-lib)
```javascript
<script runat="server">
// Com a library
var jwt = email360.jwt.encode(payload, secret);
// Sem library - implementar manualmente
</script>
```

### QR Code
```javascript
<script runat="server">
// Usar library jp-ed/qrcode-sfmc-ssjs
var qrCode = QRCode.generate('https://example.com');
</script>
```

### Bitly Links (SMS)
```javascript
%%[
SET @longUrl = "https://example.com/page"
SET @shortUrl = BitlyShorten(@longUrl, @contactKey)
]%%
<a href="%%=v(@shortUrl)=%%">Link</a>
```

## Contribuidores Notáveis

| Dev | GitHub | Contribuições |
|-----|--------|----------------|
| Jörn Berkefeld | @JoernBerkefeld | SFMC-Cookbook, eslint-config-ssjs |
| Mateusz Dabrowski | @MateuszDabrowski | DataViews schema, blog |
| MarketingThibs | @MarketingThibs | CloudPages tricks, utilities |

## Como Usar

1. Clone/baixe o repositório
2. Analise se atende necessidade
3. Implemente parcialmente (não tudo)
4. Teste em sandbox antes de produção

## Aviso

- Libraries de terceiros não são suportadas pela Salesforce
- Revise código antes de usar em produção
- Mantenha cópias locais para segurança
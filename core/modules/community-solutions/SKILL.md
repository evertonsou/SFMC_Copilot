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

### Accenture SFMC DevTools (mcdev)
- **URL**: https://github.com/Accenture/sfmc-devtools
- **Estrelas**: 155+
- **Autor**: Accenture
- **Descrição**: Rapid deployment/rollout, backup and development tool for SFMC
- **Use**: CI/CD, multi-environment deployments, backup/restore

#### Install & Setup
```bash
npm install -g mcdev
```

```javascript
import mcdev from 'mcdev';
await mcdev.init();
```

#### Supported Metadata Types
- Data Extensions, Queries, Automations, Journey Builder
- Content Builder (Assets, Images, Files)
- CloudPages, API Integrations, List Detective, Data Extract, File Transfer

#### Key Commands
```bash
mcdev retrieve -e <environment> [-m <metadataType>]
mcdev deploy -e <environment> [-m <metadataType>]
mcdev retrievetemplated -e <environment>
mcdev applyTemplate -e <environment> -t <template>
mcdev retrieveChangelog -e <environment>
```

#### Config (config.json)
```json
{
  "credentials": {
    "MY_ENV": {
      "sfmc": {
        "clientId": "xxx",
        "clientSecret": "xxx",
        "authTenant": "mytenant",
        "restTenant": "mytenant"
      }
    }
  }
}
```

#### VSCode Extension
- Marketplace: `Accenture-oss.sfmc-devtools-vscode`

#### CI/CD Integration
- Git-based deployments
- GitHub Actions, Jenkins compatible
- Child project: `sfmc-devtools-copado`

### email360/ssjs-lib
- **URL**: https://github.com/email360/ssjs-lib
- **Estrelas**: 54
- **Descrição**: Library com JWT, logging, WSProxy wrappers
- **Use**: Production-ready SSJS utilities

#### Install
```javascript
%%=TreatAsContent(HTTPGet('https://raw.githubusercontent.com/email360/ssjs-lib/master/setup/setup.ssjs'))=%%
```

#### Usage
```javascript
Platform.Load("Core", "1");
Platform.Function.ContentBlockByKey('[prefix]-ssjs-lib-[version]');
```

#### Modules
- **core**: Core utilities
- **wsproxy**: WSProxy wrappers
- **cloudpage**: CloudPages helpers
- **amp**: AMPscript interop
- **einstein**: Einstein AI integration
- **sfmcapi**: SFMC REST API wrapper
- **jwt**: JWT encode/decode/verify
- **logger**: Logging (console, json, html, DE, HTTPRequest, TriggeredSend)

#### JWT Example
```javascript
var jwt = new jwt();
jwt.encode(payload, secret);
jwt.decode(token);
jwt.verify(token, secret);
```

#### Logger Example
```javascript
var log = new logger('name');
log.level = "DEBUG";
log.configure = {
    appenders: [
        {type: "dataExtension", level: "INFO"},
        {type: "console", level: "TRACE"}
    ]
};
```

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
- **Fork de**: qrcode-sfmc (client-side) → adaptado para SSJS server-side
- **Formatos**: HTML Table, SVG

#### Usage
```javascript
<script runat="server">
Platform.Load("core", "1");

// Basic usage
var qrCodeHTMLTable = new QRCode("https://www.salesforce.com").table();
Write(qrCodeHTMLTable);

// Advanced SVG options
var qrCodeSVG = new QRCode({
    content: "https://www.salesforce.com",
    padding: 0,
    width: 300,
    height: 300,
    color: "black",
    background: "white",
    ecl: "L",
    pretty: false
}).svg();
Write("<br>");
Write(qrCodeSVG);
</script>
```

#### Options
| Option | Default | Description |
|--------|---------|-------------|
| `content` | required | Text/URL to encode |
| `width` | 300 | Width in pixels |
| `height` | 300 | Height in pixels |
| `color` | #000000 | Data modules color |
| `background` | #ffffff | Background color |
| `ecl` | L | Error correction level (L, M, Q, H) |
| `inlineStyle` | true | Use inline CSS (for HTML table) |
| `padding` | 0 | White space around image (SVG) |
| `container` | svg | svg/svg-viewbox/g/none |

### sfmc-ai-query-assistant
- **URL**: https://github.com/selimsevim/sfmc-ai-query-assistant
- **Stars**: 11
- **Autor**: @selimsevim
- **Descrição**: GPT-4o powered tool that translates natural language to SFMC SQL queries
- **Use**: Generate SQL queries from plain English descriptions

#### Features
- Natural Language to SQL conversion
- Dynamic Data Extension & Field lookup via reference tables
- SFMC SQL compliance enforcement
- Data Views handling (_job, _open, _click, _bounce)
- Error handling & query validation

#### Required Data Extensions
```sql
-- de_references: maps friendly terms to DE names
| Reference | DataExtensionName |

-- field_references: maps friendly field names
| Reference | DataExtensionName | FieldName |

-- relationships: defines DE relationships for JOINs
| DE1 | DE2 | RelationshipField |
```

#### Example
```
Input: "Find all contacts who clicked X email last week"

Output:
SELECT c.SubscriberKey, c.EventDate
FROM _click c
INNER JOIN _job j ON c.JobID = j.JobID
WHERE c.EventDate >= DATEADD(DAY, -7, GETDATE())
```

#### SQL Rules Enforced
- Allowed: SELECT, JOIN, WHERE, GROUP BY, HAVING, ROW_NUMBER
- Prohibited: INSERT, UPDATE, DELETE, WITH
- No `*` - always list fields explicitly
- Use square brackets `[DE]` for Data Extension names
- INNER JOIN and LEFT JOIN only (no CROSS JOIN)

### asset_relationship_finder
- **URL**: https://github.com/selimsevim/asset_relationship_finder
- **Stars**: 8
- **Linguagem**: Go
- **Autor**: @selimsevim
- **Descrição**: Find relationships between Data Extensions, Automations, Emails, CloudPages
- **Use**: Map asset dependencies in SFMC

#### Features
- Discover relationships between SFMC assets
- Multi-asset support: Data Extensions, Automations, Emails, Cloud Pages
- Concurrent API calls for speed
- Filter assets by name or key
- Heroku deployment

#### Required SFMC Package Permissions
- Email: Read
- Documents and Images: Read
- Saved Content: Read
- Automations: Read
- Journeys: Read
- Data Extensions: Read
- File Locations: Read

#### Environment Variables (Heroku)
```
AUTHORIZATION_URL
CLIENT_ID
CLIENT_SECRET
REDIRECT_URI
REST_ENDPOINT
SOAP_ENDPOINT
```

#### Deployment
1. Create SFMC Web App package with read permissions
2. Deploy to Heroku
3. Configure environment variables
4. Set redirect URI to https://yourherokudomain.herokuapp.com

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
| eslint-plugin-sfmc | eslint-plugin-sfmc | ESLint rules for AMPscript & SSJS |
| prettier-plugin-sfmc | prettier-plugin-sfmc | Prettier formatter for AMPscript & SSJS |
| sfmc-language | VS Code Marketplace | Full language support, diagnostics, formatting |

## Exemplos de Uso

### JWT Generation (email360/ssjs-lib)
```javascript
<script runat="server">
// Com a library
var jwt = email360.jwt.encode(payload, secret);
// Sem library - implementar manualmente
</script>
```

### QR Code (qrcode-sfmc-ssjs)
```javascript
<script runat="server">
Platform.Load("core", "1");

var qrCode = new QRCode("https://example.com").table();
Write(qrCode);

var qrCodeSVG = new QRCode({
    content: "https://example.com",
    width: 200,
    height: 200,
    ecl: "M"
}).svg();
Write(qrCodeSVG);
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
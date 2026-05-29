# Module: SFMC-Cookbook

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1.5K  
> **Repositório**: [JoernBerkefeld/sfmc-cookbook](https://github.com/JoernBerkefeld/sfmc-cookbook) ⭐ 136

---

## Visão Geral

O **SFMC-Cookbook** é um repositório de **receitas práticas** para AMPscript e SSJS no Salesforce Marketing Cloud. Criado por Jörn Berkefeld, ex-Accenture SFMC specialist. Foco em soluções reutilizáveis e best practices testadas.

---

## Categorias de Conteúdo

| Categoria | Descrição |
|-----------|-----------|
| **AMPscript Recipes** | Soluções AMPscript para casos comuns |
| **SSJS Patterns** | Padrões SSJS reutilizáveis |
| **API Integration** | Exemplos de integração REST/SOAP |
| **Data Management** | Gerenciamento de DEs e contatos |
| **Journey Patterns** | Padrões para Journey Builder |
| **Security** | Validação, sanitização e CSRF |
| **Performance** | Otimização de queries e scripts |

---

## Receitas Mais Úteis

### 1. Dynamic Content com AMPscript

```ampscript
%%[
/* Receita: Conteúdo dinâmico baseado em múltiplos atributos */
SET @segmento = Lookup("DE_Segments", "Segment", "SubscriberKey", _subscriberkey)
SET @regiao   = Lookup("DE_Segments", "Region",  "SubscriberKey", _subscriberkey)

/* Selecionar template de conteúdo correto */
SET @contentKey = CONCAT(@segmento, "_", @regiao)
SET @contentRow = LookupRows("DE_DynamicContent", "ContentKey", @contentKey)

IF RowCount(@contentRow) > 0 THEN
  SET @content = Field(Row(@contentRow, 1), "HTMLContent")
ELSE
  SET @content = Field(LookupRows("DE_DynamicContent", "ContentKey", "DEFAULT"), "HTMLContent")
ENDIF
]%%
%%=TreatAsContent(@content)=%%
```

### 2. Retry Logic para HTTPGet

```ampscript
%%[
/* Receita: Chamada HTTP com retry automático */
SET @url    = "https://api.exemplo.com/data"
SET @maxTry = 3
SET @attempt = 1
SET @success = "false"

WHILE @attempt <= @maxTry AND @success == "false" DO
  SET @response = HTTPGet(@url, "false", 0, @status)
  
  IF @status == 200 THEN
    SET @success = "true"
    SET @data = @response
  ELSE
    SET @attempt = ADD(@attempt, 1)
  ENDIF
WEND

IF @success == "false" THEN
  SET @data = "{}"  /* Default vazio em caso de falha */
ENDIF
]%%
```

### 3. Paginação em LookupRows

```ampscript
%%[
/* Receita: Iterar por todas as linhas de uma DE grande */
SET @rows = LookupRows("DE_Produtos", "Categoria", "Eletronicos")
SET @total = RowCount(@rows)

/* Limitar exibição para evitar timeout */
SET @maxDisplay = 10
SET @count = 0
]%%

%%[ FOR @i = 1 TO @total DO ]%%
%%[ IF @count < @maxDisplay THEN ]%%
  %%[ SET @row = Row(@rows, @i) ]%%
  <div>%%=Field(@row, 'Nome')=%% — R$ %%=Field(@row, 'Preco')=%%</div>
  %%[ SET @count = ADD(@count, 1) ]%%
%%[ ENDIF ]%%
%%[ NEXT @i ]%%
```

### 4. SSJS: Cache em DE para Performance

```javascript
/* Receita: Cache de dados externos em DE para reduzir chamadas API */
Platform.Load("Core", "1");

function getCachedData(cacheKey, ttlMinutes) {
  var de = DataExtension.Init("DE_Cache");
  var cached = de.Rows.Retrieve({
    Property: "CacheKey",
    SimpleOperator: "equals",
    Value: cacheKey
  });
  
  if (cached.length > 0) {
    var record = cached[0];
    var cacheTime = new Date(record.CachedAt);
    var now = new Date();
    var diffMinutes = (now - cacheTime) / 60000;
    
    if (diffMinutes < ttlMinutes) {
      return Platform.Function.ParseJSON(record.DataJSON);
    }
  }
  return null;
}

function setCachedData(cacheKey, data) {
  var de = DataExtension.Init("DE_Cache");
  de.Rows.Add({
    CacheKey: cacheKey,
    DataJSON: Stringify(data),
    CachedAt: new Date().toISOString()
  });
}

// Uso
var cacheKey = "exchange_rates_BRL";
var data = getCachedData(cacheKey, 60); // TTL: 60 minutos

if (!data) {
  // Buscar da API real
  var req = new Script.Util.HttpRequest("https://api.exchangerate.host/latest?base=USD");
  req.method = "GET";
  var response = req.send();
  data = Platform.Function.ParseJSON(response.content);
  setCachedData(cacheKey, data);
}
```

---

## Padrões de Segurança (do Cookbook)

### Sanitização de Input em CloudPages

```javascript
/* Receita: Sanitizar inputs de formulário */
Platform.Load("Core", "1");

function sanitizeInput(input) {
  if (!input) return "";
  
  // Remover tags HTML
  input = input.replace(/<[^>]*>/g, "");
  
  // Escapar caracteres especiais SQL
  input = input.replace(/'/g, "''");
  
  // Limitar tamanho
  input = input.substring(0, 255);
  
  return input.trim();
}

function validateEmail(email) {
  var pattern = /^[a-zA-Z0-9._%+\-]+@[a-zA-Z0-9.\-]+\.[a-zA-Z]{2,}$/;
  return pattern.test(email);
}

var rawEmail = Platform.Request.GetFormField("email");
var rawNome  = Platform.Request.GetFormField("nome");

if (!validateEmail(rawEmail)) {
  Variable.SetValue("@erro", "E-mail inválido");
} else {
  var safeEmail = sanitizeInput(rawEmail);
  var safeNome  = sanitizeInput(rawNome);
  // Prosseguir com dados sanitizados
}
```

---

## Links e Recursos

- **GitHub**: https://github.com/JoernBerkefeld/sfmc-cookbook
- **Issues/Contribuição**: Abrir PR com novas receitas
- **Licença**: MIT — uso livre em projetos comerciais

---

## Quando Usar o Cookbook

```
✅ Implementar padrão AMPscript não trivial → buscar receita
✅ Precisar de retry logic ou error handling robusto
✅ Cache de dados externos → padrão de DE cache
✅ Segurança em CloudPages → receitas de sanitização
✅ Dúvida sobre best practice → cookbook tem soluções testadas
```

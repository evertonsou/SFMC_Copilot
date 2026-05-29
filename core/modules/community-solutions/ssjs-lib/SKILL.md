# Module: email360/ssjs-lib

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1.5K  
> **Repositório**: [email360/ssjs-lib](https://github.com/email360/ssjs-lib) ⭐ 54

---

## Visão Geral

**ssjs-lib** é uma biblioteca SSJS open-source que abstrai operações comuns do Salesforce Marketing Cloud com uma API limpa e consistente. Reduz boilerplate e padroniza desenvolvimento SSJS.

---

## Módulos da Biblioteca

| Módulo | Descrição |
|--------|-----------|
| `lib.amp` | Execução de AMPscript dentro de SSJS |
| `lib.auth` | Autenticação REST API |
| `lib.DE` | Operações em Data Extensions |
| `lib.WSProxy` | Wrapper para WSProxy |
| `lib.HTTP` | Requisições HTTP simplificadas |
| `lib.log` | Logging padronizado |
| `lib.CT` | Content Type helpers |
| `lib.SFDC` | Integração Salesforce CRM |

---

## Instalação

### Via CloudPage (CDN da lib)

```javascript
/* Carregar a biblioteca via HTTPGet no início do script */
<script runat="server">
Platform.Load("Core", "1");

// Opção 1: Carregar da URL pública (requer acesso HTTP)
var libURL = "https://raw.githubusercontent.com/email360/ssjs-lib/master/dist/lib.min.js";
var req = new Script.Util.HttpRequest(libURL);
req.method = "GET";
var response = req.send();
eval(response.content);

// Opção 2: Copiar o código da lib para um Script Asset no Content Builder
// e referenciar via Script tag
</script>
```

### Via Code Resource (Recomendado)

1. Baixar `lib.min.js` do GitHub
2. Criar **Code Resource** no Content Builder
3. Colar o código minificado
4. Referenciar na CloudPage:

```html
<script src="%%=CloudPagesURL(ID_DO_CODE_RESOURCE)=%%" runat="server"></script>
```

---

## Uso — Data Extension (lib.DE)

### Operações CRUD Simplificadas

```javascript
<script runat="server">
// Após carregar a lib...

// READ — Buscar registro
var cliente = lib.DE.getRow("DE_Clientes", "Email", "cliente@exemplo.com");
Write("Nome: " + cliente.FirstName);

// CREATE — Inserir registro
lib.DE.upsert("DE_Clientes", {
  Email: "novo@cliente.com",
  FirstName: "Maria",
  LastName: "Silva",
  Tier: "Gold",
  CreatedAt: new Date().toISOString()
}, "Email");

// UPDATE — Atualizar campo
lib.DE.update("DE_Clientes", "Email", "cliente@exemplo.com", {
  Tier: "Platinum",
  UpdatedAt: new Date().toISOString()
});

// DELETE — Remover linha
lib.DE.deleteRow("DE_Clientes", "Email", "cliente@exemplo.com");

// QUERY — Buscar múltiplas linhas com filtro
var goldClientes = lib.DE.getRows("DE_Clientes", {
  filter: {
    Property: "Tier",
    SimpleOperator: "equals",
    Value: "Gold"
  }
});
</script>
```

---

## Uso — HTTP Requests (lib.HTTP)

```javascript
<script runat="server">
// GET simplificado
var response = lib.HTTP.get("https://api.exemplo.com/produtos", {
  headers: {
    "Authorization": "Bearer " + token,
    "Content-Type": "application/json"
  }
});

if (response.statusCode === 200) {
  var data = response.json();
  Write("Total de produtos: " + data.total);
}

// POST simplificado
var postResult = lib.HTTP.post("https://api.exemplo.com/orders", {
  body: {
    customerId: "12345",
    items: [{ productId: "A1", qty: 2 }]
  },
  headers: {
    "Authorization": "Bearer " + token
  }
});
</script>
```

---

## Uso — Logging (lib.log)

```javascript
<script runat="server">
// Configurar log
lib.log.setLevel("DEBUG"); // DEBUG, INFO, WARN, ERROR

// Logs estruturados
lib.log.debug("Iniciando processamento", { subscriberKey: subKey });
lib.log.info("Subscriber encontrado", { email: email, tier: tier });
lib.log.warn("Dado opcional ausente", { field: "Phone" });
lib.log.error("Falha na API", { statusCode: 500, message: err.message });

// Salvar logs em DE (opcional)
lib.log.saveToDE("DE_AppLogs", {
  includeTimestamp: true,
  includeLevel: true
});
</script>
```

---

## Uso — AMPscript dentro de SSJS (lib.amp)

```javascript
<script runat="server">
// Executar função AMPscript e capturar resultado
var email = lib.amp.execute('AttributeValue("emailaddr")');
var subKey = lib.amp.execute('_subscriberkey');

// Avaliar AMPscript completo como string
var result = lib.amp.evaluate(%%[
  SET @nome = Lookup("DE_Profiles", "FirstName", "Email", @email)
  Output(v(@nome))
]%%);

Write("Nome via AMPscript: " + result);
</script>
```

---

## Uso — WSProxy Wrapper (lib.WSProxy)

```javascript
<script runat="server">
// Retrieve simplificado
var subscribers = lib.WSProxy.retrieve("Subscriber", 
  ["EmailAddress", "Status", "SubscriberKey"],
  {
    Property: "Status",
    SimpleOperator: "equals",
    Value: "Active"
  }
);

Write("Ativos encontrados: " + subscribers.length);

// Create/Update
lib.WSProxy.upsert("Subscriber", {
  EmailAddress: "novo@email.com",
  SubscriberKey: "sub-12345",
  Status: "Active"
});
</script>
```

---

## Padrão Completo: Form Handler com ssjs-lib

```javascript
<script runat="server">
Platform.Load("Core", "1");
// [lib carregada aqui]

var email = Platform.Request.GetFormField("email");
var nome  = Platform.Request.GetFormField("nome");

// Validar
if (!email || !email.match(/^[^@]+@[^@]+\.[^@]+$/)) {
  lib.log.warn("Email inválido recebido", { email: email });
  Variable.SetValue("@erro", "E-mail inválido");
} else {
  try {
    // Upsert subscriber
    lib.DE.upsert("DE_FormLeads", {
      Email:       email,
      FirstName:   nome,
      SourcePage:  "Landing_Promo_Jun",
      SubmittedAt: new Date().toISOString()
    }, "Email");
    
    lib.log.info("Lead capturado", { email: email });
    Variable.SetValue("@sucesso", "true");
    
  } catch(e) {
    lib.log.error("Falha ao salvar lead", { error: e.message, email: email });
    Variable.SetValue("@erro", "Erro interno. Tente novamente.");
  }
}
</script>

%%[ IF @sucesso == "true" THEN ]%%
<p>✅ Cadastro realizado!</p>
%%[ ELSE ]%%
<p style="color:red;">%%=v(@erro)=%%</p>
%%[ ENDIF ]%%
```

---

## Quando Usar ssjs-lib

```
✅ Projetos com muito código SSJS repetitivo
✅ Equipes que precisam de padrão consistente
✅ Logging estruturado em produção
✅ Operações CRUD frequentes em DEs
✅ Integração com APIs externas
❌ Scripts simples e pontuais (overhead não justifica)
❌ Ambientes onde download de URL externa é bloqueado
```

---

## Links e Recursos

- **GitHub**: https://github.com/email360/ssjs-lib
- **Documentação**: https://github.com/email360/ssjs-lib/wiki
- **npm**: https://www.npmjs.com/package/ssjs-lib
- **Exemplos**: https://github.com/email360/ssjs-lib/tree/master/examples

# Module: Subscriber Management

> **Skill**: sfmc-copilot | **Categoria**: List Management | **Tokens**: ~2K

---

## Visão Geral

**Subscriber Management** cobre o ciclo de vida completo de um subscriber: cadastro, gerenciamento de listas, estados de inscrição, publicação e desinscrição. Base essencial para compliance e deliverability.

---

## Subscriber vs. Contact

| Conceito | Descrição |
|----------|-----------|
| **Subscriber** | Registro de e-mail associado a uma lista |
| **Contact** | Perfil unificado no Contact Builder (multi-canal) |
| **SubscriberKey** | Identificador único do subscriber no SFMC |
| **EmailAddress** | Endereço de e-mail do subscriber |

> **Regra**: `SubscriberKey` ≠ `EmailAddress` — use um ID estável como SubscriberKey.

---

## Estados (Status) de Subscriber

| Status | Recebe Emails? | Descrição |
|--------|---------------|-----------|
| `Active` | ✅ Sim | Inscrito normalmente |
| `Unsubscribed` | ❌ Não | Optou por sair |
| `Held` | ❌ Não | Bloqueado por bounce excessivo |
| `Bounced` | ❌ Não | Hard bounce registrado |
| `Deleted` | ❌ Não | Removido do sistema |

### Transições de Status

```
Active → Unsubscribed  : Clicou em unsubscribe
Active → Held          : 3+ soft bounces ou 1 hard bounce
Active → Bounced       : Hard bounce confirmado
Unsubscribed → Active  : Re-opt-in (precisa de confirm. explícita)
Held → Active          : Reset manual pelo admin
```

---

## Publication Lists vs. Subscriber Lists

| Tipo | Descrição | Uso |
|------|-----------|-----|
| **Subscriber List** | Lista de contatos (All Subscribers) | Repositório global |
| **Publication List** | Lista de preferência por tipo de conteúdo | Opt-in por categoria |
| **Suppression List** | Lista de exclusão do envio | Compliance |
| **Group** | Subconjunto de uma lista | Segmentação simples |

### Criar Publication List

1. Email Studio → **Subscribers** → **Lists** → **Create**
2. Tipo: **Publication List**
3. Nome: `PubList_Newsletter_Mensal`
4. Configurar auto-unsubscribe behavior
5. Salvar

---

## Gerenciar Subscribers via AMPscript

### Verificar Status

```ampscript
%%[
SET @email = "contato@empresa.com"
SET @status = Lookup("_Subscribers", "Status", "EmailAddress", @email)

IF @status == "Active" THEN
  SET @mensagem = "Subscriber ativo"
ELSEIF @status == "Unsubscribed" THEN
  SET @mensagem = "Subscriber desinscrito"
ELSEIF @status == "Held" THEN
  SET @mensagem = "Subscriber bloqueado por bounces"
ELSE
  SET @mensagem = "Subscriber não encontrado"
ENDIF
]%%
%%=v(@mensagem)=%%
```

### Assinar em Publication List (CloudPage)

```ampscript
%%[
SET @email  = RequestParameter("email")
SET @nome   = RequestParameter("nome")
SET @listID = "12345"  /* ID da Publication List */

IF NOT Empty(@email) THEN
  /* Upsert subscriber */
  SET @subResult = UpsertData(
    "Email Addresses",
    1, "Email Address", @email,
    "First Name", @nome
  )
  
  /* Inscrever na Publication List */
  SET @listResult = InsertDE(
    "ListSubscribers",
    "SubscriberKey", @email,
    "ListID",        @listID,
    "Status",        "Active"
  )
  
  SET @sucesso = "true"
ENDIF
]%%

%%[ IF @sucesso == "true" THEN ]%%
<p>✅ Inscrição realizada com sucesso!</p>
%%[ ENDIF ]%%
```

---

## Gerenciar Subscribers via SSJS

### Upsert de Subscriber

```javascript
<script runat="server">
Platform.Load("Core", "1");

var api = new Script.Util.WSProxy();

function upsertSubscriber(email, subKey, attributes, listID) {
  var sub = {
    EmailAddress: email,
    SubscriberKey: subKey,
    Status: "Active",
    Attributes: attributes || [],
    Lists: listID ? [{ ID: listID, Status: "Active" }] : []
  };

  // Tentar criar; se existir, atualiza (upsert)
  var result = api.update("Subscriber", sub, {
    SaveOptions: [{ PropertyName: "*", SaveAction: "UpdateAdd" }]
  }, null);

  return result;
}

// Uso
var email   = Platform.Request.GetFormField("email");
var subKey  = Platform.Request.GetFormField("subKey") || email;
var nome    = Platform.Request.GetFormField("nome");

var attrs = [
  { Name: "First Name", Value: nome }
];

var result = upsertSubscriber(email, subKey, attrs, 12345);

if (result.Status === "OK") {
  Variable.SetValue("@sucesso", "true");
} else {
  Variable.SetValue("@erro", result.StatusMessage);
}
</script>
```

### Desinscrever de Lista Específica

```javascript
<script runat="server">
Platform.Load("Core", "1");

var api = new Script.Util.WSProxy();

function unsubFromList(subKey, listID) {
  var sub = {
    SubscriberKey: subKey,
    Lists: [{ ID: listID, Status: "Unsubscribed" }]
  };
  return api.update("Subscriber", sub, null, null);
}

// Desinscrever apenas da newsletter (não unsubscribe global)
var result = unsubFromList("12345-sub-key", 67890);
Write(Stringify(result));
</script>
```

---

## SQL — Queries de Subscriber Management

### Análise do Estado da Base

```sql
SELECT
  Status,
  COUNT(*)                                    AS Total,
  CAST(COUNT(*) AS FLOAT) /
    SUM(COUNT(*)) OVER() * 100               AS Percentual
FROM _Subscribers
GROUP BY Status
ORDER BY Total DESC
```

### Novos Subscribers por Mês

```sql
SELECT
  FORMAT(DateJoined, 'yyyy-MM') AS Mes,
  COUNT(*)                      AS NovosSubscribers
FROM _Subscribers
WHERE DateJoined >= DATEADD(MONTH, -12, GETDATE())
GROUP BY FORMAT(DateJoined, 'yyyy-MM')
ORDER BY Mes DESC
```

### Subscribers por Lista

```sql
SELECT
  ls.ListID,
  l.ListName,
  COUNT(DISTINCT ls.SubscriberKey) AS TotalSubscribers,
  SUM(CASE WHEN ls.Status = 'Active' THEN 1 ELSE 0 END) AS Ativos,
  SUM(CASE WHEN ls.Status = 'Unsubscribed' THEN 1 ELSE 0 END) AS Desinscritos
FROM _ListSubscribers ls
JOIN _List l ON ls.ListID = l.ID
GROUP BY ls.ListID, l.ListName
ORDER BY TotalSubscribers DESC
```

### Re-opt-in — Subscribers Inativos para Reativação

```sql
SELECT
  s.SubscriberKey,
  s.EmailAddress,
  s.DateJoined,
  MAX(o.EventDate) AS LastOpen,
  DATEDIFF(DAY, MAX(ISNULL(o.EventDate, s.DateJoined)), GETDATE()) AS DaysInactive
FROM _Subscribers s
LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey
WHERE s.Status = 'Active'
GROUP BY s.SubscriberKey, s.EmailAddress, s.DateJoined
HAVING DATEDIFF(DAY, MAX(ISNULL(o.EventDate, s.DateJoined)), GETDATE()) BETWEEN 180 AND 365
ORDER BY DaysInactive DESC
```

---

## Double Opt-in (DOI)

### Fluxo Recomendado

```
1. Usuário preenche formulário
2. Subscriber criado com Status = "Pending" (customizado via DE)
3. E-mail de confirmação enviado com link único
4. Usuário clica no link → CloudPage de confirmação
5. Status atualizado para "Active"
6. Confirmação enviada
```

### CloudPage de Confirmação DOI

```ampscript
%%[
SET @token = QueryParameter("token")

/* Buscar subscriber pelo token */
SET @row = LookupRows("DE_DOI_Pending", "Token", @token)

IF RowCount(@row) > 0 THEN
  SET @r      = Row(@row, 1)
  SET @email  = Field(@r, "EmailAddress")
  SET @subKey = Field(@r, "SubscriberKey")
  
  /* Ativar subscriber */
  SET @result = UpsertData(
    "Email Addresses",
    1, "Email Address", @email,
    "Status", "Active",
    "DOI_Confirmed", "true",
    "DOI_Date", Now()
  )
  
  /* Remover token da DE pending */
  SET @del = DeleteData("DE_DOI_Pending", "Token", @token)
  
  SET @confirmado = "true"
ELSE
  SET @erro = "Token inválido ou expirado."
ENDIF
]%%

%%[ IF @confirmado == "true" THEN ]%%
<h1>✅ Inscrição Confirmada!</h1>
<p>Seu e-mail %%=v(@email)=%% foi confirmado com sucesso.</p>
%%[ ELSE ]%%
<h1>⚠️ Erro na Confirmação</h1>
<p>%%=v(@erro)=%%</p>
%%[ ENDIF ]%%
```

---

## Boas Práticas

```
✅ Sempre use SubscriberKey diferente do e-mail (ex: CRM ID)
✅ Implemente double opt-in para novos cadastros (melhora qualidade)
✅ Monitore subscribers "Held" — podem ser reativados manualmente
✅ Nunca reative um Unsubscribed sem novo consentimento explícito
✅ Use Publication Lists para preferências granulares
✅ Mantenha log de todas as mudanças de status (compliance)
✅ Execute a sunset policy para inativos com mais de 180 dias
✅ Audite duplicatas mensalmente (mesmo email, SubscriberKeys diferentes)
```

---

## Referências

- [Subscribers Help](https://help.salesforce.com/s/articleView?id=sf.mc_es_subscribers.htm)
- [Publication Lists](https://help.salesforce.com/s/articleView?id=sf.mc_es_publication_lists.htm)
- [_Subscribers DataView](https://help.salesforce.com/s/articleView?id=sf.mc_as_data_view_subscribers.htm)
- [_ListSubscribers DataView](https://help.salesforce.com/s/articleView?id=sf.mc_as_data_view_list_subscribers.htm)

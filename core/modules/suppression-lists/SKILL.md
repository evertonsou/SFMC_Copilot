# Module: Suppression Lists

> **Skill**: sfmc-copilot | **Categoria**: Deliverability & Compliance | **Tokens**: ~2K

---

## Visão Geral

**Suppression Lists** são listas de contatos que **nunca devem receber** determinado tipo de comunicação. Funcionam como filtros automáticos antes do envio, garantindo conformidade legal e proteção da reputação do remetente.

---

## Tipos de Suppression Lists

### 1. Global Unsubscribe (All Subscribers)
- Lista automática gerenciada pelo SFMC
- Contatos que clicaram em "Unsubscribe" em qualquer e-mail
- **Escopo**: Toda a conta (todas as BUs)
- **Não pode ser contornada** — proteção máxima

### 2. Master Suppression List (MSL)
- Lista global de supressão customizável
- **Escopo**: Toda a conta (todas as BUs)
- Ideal para: reclamações, bounces permanentes, opt-outs legais

### 3. BU-Level Suppression
- Supressão específica por Business Unit
- **Escopo**: Apenas a BU configurada
- Ideal para: opt-outs de produto específico, segmentos regionais

### 4. List-Level Exclusion
- Exclusão via filtro na hora do envio
- Baseada em campos da DE ou Status de Subscriber
- Configurada no **Send Flow** ou **Journey Builder**

### 5. Publication Lists (Subscription)
- Assinantes optam por receber categorias específicas
- Desinscrição de uma lista não afeta outras

---

## Configurar Master Suppression List

### Passo a Passo

1. **Admin** → **Account Settings** → **Master Suppression List**
2. Criar ou importar lista (CSV)
3. Mapear coluna de e-mail ou ContactKey
4. Ativar

### Formato CSV para Import

```csv
EmailAddress,Reason,DateAdded
contato1@email.com,Complaint,2026-01-15
contato2@email.com,Legal Request,2026-01-20
contato3@email.com,Hard Bounce,2026-01-22
```

---

## Suppression via Data Extension

### Criar DE de Supressão

```sql
-- Estrutura recomendada da Suppression DE
Nome: DE_Suppression_Global
Campos:
  - EmailAddress (Email, PK)
  - Reason (Text 100)
  - AddedDate (Date)
  - Source (Text 50)
```

### Usar como Exclusion List no Send Flow

1. Email Studio → **Send** → **Exclusions**
2. **Exclude based on Data Extension**
3. Selecionar `DE_Suppression_Global`
4. Campo: `EmailAddress` = `%%emailaddr%%`

### Usar em Journey Builder

1. No Journey → **Send Email Activity** → **Advanced**
2. **Suppression List** → Adicionar DE
3. Configurar campo de match

---

## Exclusion Logic via AMPscript

### Verificar se contato está suprimido antes do envio

```ampscript
%%[
/* Verificar na suppression list antes de continuar */
SET @email = AttributeValue("EmailAddress")
SET @suprimido = Lookup("DE_Suppression_Global", "EmailAddress", "EmailAddress", @email)

IF NOT Empty(@suprimido) THEN
  /* Redirecionar para página de erro ou simplesmente não renderizar */
  RaiseError("Contato suprimido - envio cancelado", 1)
ENDIF
]%%
```

### Adicionar à Suppression List via AMPscript (CloudPage)

```ampscript
%%[
SET @email = RequestParameter("email")
SET @motivo = RequestParameter("reason")

IF NOT Empty(@email) THEN
  SET @rows = InsertData(
    "DE_Suppression_Global",
    "EmailAddress", @email,
    "Reason", @motivo,
    "AddedDate", Now(),
    "Source", "CloudPage_Form"
  )
  
  IF @rows > 0 THEN
    SET @sucesso = "true"
  ENDIF
ENDIF
]%%

%%[ IF @sucesso == "true" THEN ]%%
  <p>Você foi removido com sucesso.</p>
%%[ ELSE ]%%
  <p>Erro ao processar solicitação.</p>
%%[ ENDIF ]%%
```

---

## SSJS — Gerenciar Suppression List

### Adicionar contato via WSProxy

```javascript
<script runat="server">
Platform.Load("Core", "1");

var api = new Script.Util.WSProxy();

function addToSuppressionList(email, reason) {
  var de = {
    CustomerKey: "DE_Suppression_Global",
    Properties: [
      { Name: "EmailAddress", Value: email },
      { Name: "Reason", Value: reason },
      { Name: "AddedDate", Value: new Date().toISOString() },
      { Name: "Source", Value: "SSJS_Script" }
    ]
  };
  
  var result = api.createItem("DataExtensionObject", de);
  return result;
}

// Uso
var email = Platform.Request.GetFormField("email");
var result = addToSuppressionList(email, "User Request");

if (result.Status === "OK") {
  Write(Stringify({ success: true }));
} else {
  Write(Stringify({ success: false, error: result.StatusMessage }));
}
</script>
```

### Verificar Status de Subscriber

```javascript
<script runat="server">
Platform.Load("Core", "1");

var api = new Script.Util.WSProxy();

function getSubscriberStatus(email) {
  var cols = ["EmailAddress", "Status", "UnsubscribedDate"];
  var filter = {
    Property: "EmailAddress",
    SimpleOperator: "equals",
    Value: email
  };
  
  var result = api.retrieve("Subscriber", cols, filter);
  
  if (result.Results.length > 0) {
    return result.Results[0];
  }
  return null;
}

var subscriber = getSubscriberStatus("contato@email.com");
if (subscriber && subscriber.Status === "Unsubscribed") {
  Write("Contato está desinscrito desde: " + subscriber.UnsubscribedDate);
}
</script>
```

---

## SQL — Auditorias de Supressão

### Listar todos os unsubscribes recentes

```sql
SELECT
  s.SubscriberKey,
  s.EmailAddress,
  u.EventDate AS UnsubDate,
  u.List,
  u.Reason
FROM _Unsubscribe u
JOIN _Subscribers s ON u.SubscriberKey = s.SubscriberKey
WHERE u.EventDate >= DATEADD(DAY, -30, GETDATE())
ORDER BY u.EventDate DESC
```

### Bounces permanentes para adicionar à suppression

```sql
SELECT DISTINCT
  b.EmailAddress,
  b.SubscriberKey,
  b.BounceType,
  MIN(b.EventDate) AS FirstBounce,
  COUNT(*) AS TotalBounces
FROM _Bounce b
WHERE b.BounceType = 'HardBounce'
GROUP BY b.EmailAddress, b.SubscriberKey, b.BounceType
HAVING COUNT(*) >= 2
ORDER BY TotalBounces DESC
```

### Cruzar envios com suppression list

```sql
-- Verificar se algum contato suprimido recebeu e-mail por engano
SELECT
  se.EmailAddress,
  se.EventDate AS SendDate,
  sl.Reason AS SuppressionReason
FROM _Sent se
JOIN DE_Suppression_Global sl ON se.EmailAddress = sl.EmailAddress
WHERE se.EventDate >= DATEADD(DAY, -30, GETDATE())
```

---

## Tipos de Status de Subscriber

| Status | Descrição |
|--------|-----------|
| `Active` | Pode receber e-mails |
| `Unsubscribed` | Optou por sair — não recebe |
| `Held` | Bounces excessivos — bloqueado temporariamente |
| `Bounced` | Hard bounce — bloqueado |
| `Deleted` | Removido do sistema |

---

## Boas Práticas

```
✅ Sempre mantenha a MSL atualizada — importe bounces e reclamações semanalmente
✅ Configure supressão no nível do Journey, não apenas no envio manual
✅ Documente o motivo de cada supressão (LGPD/GDPR compliance)
✅ Crie alertas automáticos quando a lista cresce acima do esperado
✅ Nunca envie para contatos com status Unsubscribed ou Held
✅ Revise a lista trimestralmente para remover entradas obsoletas
✅ Mantenha logs de auditoria — quem adicionou, quando e por quê
✅ Sincronize com o CRM para supressões bidirecionais
```

---

## Conformidade Legal

| Regulação | Requisito |
|-----------|-----------|
| **CAN-SPAM (EUA)** | 10 dias para processar opt-out |
| **LGPD (Brasil)** | Imediato — sem prazo de tolerância |
| **GDPR (Europa)** | Imediato + direito ao esquecimento |
| **CASL (Canadá)** | Express consent obrigatório |

---

## Referências

- [Suppression Lists Help](https://help.salesforce.com/s/articleView?id=sf.mc_es_suppression_lists.htm)
- [Master Suppression List](https://help.salesforce.com/s/articleView?id=sf.mc_es_master_suppression_list.htm)
- [_Unsubscribe DataView](https://help.salesforce.com/s/articleView?id=sf.mc_as_data_view_unsubscribe.htm)

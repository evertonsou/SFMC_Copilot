# Module: Custom Unsubscribe

> **Skill**: sfmc-copilot | **Categoria**: Compliance & Subscriber Management | **Tokens**: ~2K

---

## Visão Geral

O **Custom Unsubscribe** permite criar páginas de desinscrição personalizadas que substituem a página padrão do SFMC. Oferece controle total sobre UX, lógica de negócio e conformidade legal.

---

## Fluxo Padrão vs. Customizado

| Aspecto | Padrão SFMC | Customizado |
|---------|-------------|-------------|
| Design | Genérico | Branded |
| Opções | Unsubscribe All | Granular por categoria |
| Lógica | Automática | Controlada via código |
| Rastreamento | Básico | Completo com logs |
| Conformidade | CAN-SPAM | CAN-SPAM + LGPD + GDPR |

---

## Configurar Link de Unsubscribe Customizado

### No E-mail (substituir link padrão)

```ampscript
%%[
/* Gerar URL da CloudPage de unsubscribe com parâmetros do subscriber */
SET @unsubURL = CloudPagesURL(
  456,
  "sub",   _subscriberkey,
  "email", emailaddr,
  "job",   _JobID,
  "list",  _ListID,
  "bu",    _businessunitid
)
]%%

<a href="%%=v(@unsubURL)=%%">Cancelar inscrição</a>
```

> ⚠️ **Obrigatório**: Todo e-mail comercial deve ter link de unsubscribe funcional (CAN-SPAM, LGPD).

---

## Página de Unsubscribe — AMPscript Completo

```ampscript
%%[
/* ===== CUSTOM UNSUBSCRIBE PAGE ===== */

/* 1. Capturar parâmetros */
SET @subKey = QueryParameter("sub")
SET @email  = QueryParameter("email")
SET @jobID  = QueryParameter("job")
SET @listID = QueryParameter("list")

/* 2. Validação de segurança */
IF Empty(@subKey) OR Empty(@email) THEN
  SET @erro = "Link inválido. Por favor, acesse pelo link do e-mail recebido."
ELSE
  /* 3. Verificar se já está desinscrito */
  SET @subStatus = Lookup("_Subscribers", "Status", "SubscriberKey", @subKey)
  
  IF @subStatus == "Unsubscribed" THEN
    SET @jaDesinscrito = "true"
  ENDIF
ENDIF
]%%

<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cancelar Inscrição</title>
</head>
<body>

%%[ IF NOT Empty(@erro) THEN ]%%
  <div class="error">
    <h2>⚠️ Link Inválido</h2>
    <p>%%=v(@erro)=%%</p>
    <p>Por favor, use o link diretamente do e-mail que recebeu.</p>
  </div>
%%[ ELSEIF @jaDesinscrito == "true" THEN ]%%
  <div class="info">
    <h2>✅ Você já está desinscrito</h2>
    <p>O e-mail <strong>%%=v(@email)=%%</strong> já não recebe nossas comunicações.</p>
  </div>
%%[ ELSE ]%%
  <h1>Cancelar Inscrição</h1>
  <p>Você está cancelando a inscrição de: <strong>%%=v(@email)=%%</strong></p>
  
  <form method="POST" action="%%=CloudPagesURL(457)=%%">
    <input type="hidden" name="subKey" value="%%=v(@subKey)=%%">
    <input type="hidden" name="email"  value="%%=v(@email)=%%">
    <input type="hidden" name="jobID"  value="%%=v(@jobID)=%%">
    <input type="hidden" name="listID" value="%%=v(@listID)=%%">
    
    <p>Por que está saindo? (opcional)</p>
    <select name="reason">
      <option value="too_many">Recebo e-mails demais</option>
      <option value="not_relevant">Conteúdo não é relevante</option>
      <option value="never_signed">Nunca me cadastrei</option>
      <option value="privacy">Questões de privacidade</option>
      <option value="other">Outro motivo</option>
    </select>
    
    <button type="submit" name="action" value="unsubscribe">
      Confirmar Cancelamento
    </button>
    <a href="%%=CloudPagesURL(123)=%%">Prefiro gerenciar minhas preferências</a>
  </form>
%%[ ENDIF ]%%

</body>
</html>
```

---

## Handler de Unsubscribe — AMPscript

```ampscript
%%[
/* ===== UNSUBSCRIBE HANDLER ===== */

SET @subKey = RequestParameter("subKey")
SET @email  = RequestParameter("email")
SET @jobID  = RequestParameter("jobID")
SET @listID = RequestParameter("listID")
SET @reason = RequestParameter("reason")
SET @action = RequestParameter("action")

/* Validações */
IF Empty(@subKey) OR Empty(@email) THEN
  SET @erro = "Dados inválidos."
ELSEIF @action != "unsubscribe" THEN
  SET @erro = "Ação inválida."
ELSE
  /* Executar unsubscribe via função nativa */
  SET @result = InvokeDelete(
    "Subscriber",
    1,
    "SubscriberKey", @subKey,
    "EmailAddress",   @email
  )
  
  /* Logar o unsubscribe na DE de auditoria */
  SET @logResult = InsertData(
    "DE_Unsubscribe_Log",
    "SubscriberKey", @subKey,
    "Email",         @email,
    "JobID",         @jobID,
    "Reason",        @reason,
    "UnsubDate",     Now(),
    "Source",        "CloudPage"
  )
  
  SET @sucesso = "true"
ENDIF
]%%

%%[ IF NOT Empty(@erro) THEN ]%%
  <h2>⚠️ Erro</h2>
  <p>%%=v(@erro)=%%</p>
%%[ ELSEIF @sucesso == "true" THEN ]%%
  <h2>✅ Inscrição Cancelada</h2>
  <p>O e-mail <strong>%%=v(@email)=%%</strong> foi removido com sucesso.</p>
  <p>Você não receberá mais nossas comunicações.</p>
  <p>Mudou de ideia? <a href="%%=CloudPagesURL(123)=%%">Gerencie suas preferências</a>.</p>
%%[ ENDIF ]%%
```

---

## Handler de Unsubscribe — SSJS (Mais Robusto)

```javascript
<script runat="server">
Platform.Load("Core", "1");

var subKey = Platform.Request.GetFormField("subKey");
var email  = Platform.Request.GetFormField("email");
var reason = Platform.Request.GetFormField("reason");
var action = Platform.Request.GetFormField("action");

// Validação de segurança
if (!subKey || !email || action !== "unsubscribe") {
  Variable.SetValue("@erro", "Dados inválidos.");
} else {
  try {
    var api = new Script.Util.WSProxy();
    
    // 1. Unsubscribe via WSProxy
    var subObj = {
      EmailAddress: email,
      SubscriberKey: subKey,
      Status: "Unsubscribed"
    };
    
    var updateResult = api.update("Subscriber", subObj, null, null);
    
    if (updateResult.Status === "OK" || updateResult.Status === "OverallStatus") {
      // 2. Logar auditoria
      var logDE = DataExtension.Init("DE_Unsubscribe_Log");
      logDE.Rows.Add({
        SubscriberKey: subKey,
        Email: email,
        Reason: reason || "Not provided",
        UnsubDate: Platform.Function.SystemDateToLocalDate(new Date()),
        Source: "CloudPage_SSJS",
        IPAddress: Platform.Request.ClientIP()
      });
      
      Variable.SetValue("@sucesso", "true");
      Variable.SetValue("@email", email);
    } else {
      Variable.SetValue("@erro", "Falha ao processar. Tente novamente.");
    }
    
  } catch(e) {
    Variable.SetValue("@erro", "Erro interno: " + e.message);
  }
}
</script>

%%[ IF NOT Empty(@erro) THEN ]%%
  <p style="color:red;">%%=v(@erro)=%%</p>
%%[ ELSEIF @sucesso == "true" THEN ]%%
  <h2>✅ Desinscrito com Sucesso</h2>
  <p>%%=v(@email)=%% foi removido.</p>
%%[ ENDIF ]%%
```

---

## DE de Log de Auditoria

```
Nome: DE_Unsubscribe_Log
Campos:
  SubscriberKey  Text(36)   PK
  Email          Email
  Reason         Text(100)
  UnsubDate      Date
  Source         Text(50)    (CloudPage, API, Manual, Journey)
  IPAddress      Text(50)
  JobID          Text(50)
  ListID         Text(50)
```

---

## Segurança — Boas Práticas

```
✅ Valide SEMPRE que subKey e email correspondem (_Subscribers)
✅ Nunca confie apenas no QueryParameter sem verificação no servidor
✅ Use HTTPS em todas as CloudPages de unsubscribe
✅ Implemente rate limiting para evitar abuse (ex: múltiplos unsubscribes sequenciais)
✅ Logue IP, timestamp e motivo para compliance LGPD/GDPR
✅ Não exponha subKey na URL de confirmação (use token temporário)
✅ Confirme o unsubscribe por e-mail transacional (sem links de marketing)
```

### Validação Cruzada de Segurança

```ampscript
%%[
/* Validar que subKey corresponde ao email para evitar unsubscribe indevido */
SET @emailVerify = Lookup("_Subscribers", "EmailAddress", "SubscriberKey", @subKey)

IF LowerCase(@emailVerify) != LowerCase(@email) THEN
  SET @erro = "Dados inconsistentes. Link inválido."
ENDIF
]%%
```

---

## Referências

- [Unsubscribe Help](https://help.salesforce.com/s/articleView?id=sf.mc_es_unsubscribe.htm)
- [InvokeDelete AMPscript](https://ampscript.guide/invokedelete/)
- [CloudPagesURL Function](https://ampscript.guide/cloudpagesurl/)
- [CAN-SPAM Requirements](https://www.ftc.gov/business-guidance/resources/can-spam-act-compliance-guide-business)

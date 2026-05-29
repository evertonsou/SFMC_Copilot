# Module: Preference Centers

> **Skill**: sfmc-copilot | **Categoria**: Subscriber Management | **Tokens**: ~2.5K

---

## Visão Geral

**Preference Centers** são páginas que permitem ao assinante controlar **quais comunicações deseja receber**, sem precisar se desinscrever completamente. Aumentam a retenção e reduzem unsubscribes.

---

## Abordagens Disponíveis

| Abordagem | Descrição | Quando Usar |
|-----------|-----------|-------------|
| **Profile Center Nativo** | Página padrão do SFMC | Implementação rápida, sem customização |
| **CloudPage Customizada** | HTML/CSS/AMPscript total | Controle total de UI e lógica |
| **CloudPage + SSJS** | Form handler robusto | Formulários complexos, validações avançadas |

---

## Profile Center Nativo

### Link de Acesso (AMPscript)
```ampscript
%%[ SET @profileCenter = ProfileCenter() ]%%
<a href="%%=v(@profileCenter)=%%">Gerenciar Preferências</a>
```

### Personalização Limitada
- Estilo via CSS customizado no **Email Studio → Sender Profiles**
- Campos exibidos configuráveis no **Admin → Profile Attributes**

---

## CloudPage Customizada — Estrutura Completa

### Arquitetura Recomendada

```
cloudpage-preference-center/
├── preference-center.html     (página principal)
├── handler.html               (processa POST)
└── confirmation.html          (página de sucesso)
```

### Preference Center — AMPscript Completo

```ampscript
%%[
/* ===== PREFERENCE CENTER COM AMPSCRIPT ===== */

/* 1. Capturar token do subscriber (via link no e-mail) */
SET @subKey = QueryParameter("sub")
SET @email = QueryParameter("email")

/* 2. Decodificar valores se vieram do e-mail */
IF Empty(@subKey) THEN
  SET @subKey = AttributeValue("_subscriberkey")
ENDIF

IF Empty(@email) THEN
  SET @email = AttributeValue("emailaddr")
ENDIF

/* 3. Buscar preferências atuais na DE */
SET @prefRow = LookupRows("DE_EmailPreferences", "SubscriberKey", @subKey)

IF RowCount(@prefRow) > 0 THEN
  SET @pref = Row(@prefRow, 1)
  SET @pNewsletter     = Field(@pref, "Newsletter")
  SET @pPromocoes      = Field(@pref, "Promocoes")
  SET @pTransacional   = Field(@pref, "Transacional")
  SET @pAlertas        = Field(@pref, "Alertas")
ELSE
  /* Defaults — tudo ativo */
  SET @pNewsletter   = "true"
  SET @pPromocoes    = "true"
  SET @pTransacional = "true"
  SET @pAlertas      = "true"
ENDIF
]%%

<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Preferências de E-mail</title>
</head>
<body>
<h1>Suas Preferências de Comunicação</h1>
<p>Olá! Gerencie quais e-mails deseja receber.</p>

<form method="POST" action="%%=CloudPagesURL(123)=%%">
  <input type="hidden" name="subKey" value="%%=v(@subKey)=%%">
  <input type="hidden" name="email"  value="%%=v(@email)=%%">

  <label>
    <input type="checkbox" name="newsletter" value="true"
      %%[ IF @pNewsletter == "true" THEN ]%% checked %%[ ENDIF ]%%>
    Newsletter Mensal
  </label><br>

  <label>
    <input type="checkbox" name="promocoes" value="true"
      %%[ IF @pPromocoes == "true" THEN ]%% checked %%[ ENDIF ]%%>
    Promoções e Ofertas
  </label><br>

  <label>
    <input type="checkbox" name="transacional" value="true"
      %%[ IF @pTransacional == "true" THEN ]%% checked %%[ ENDIF ]%%>
    E-mails Transacionais (confirmações, recibos)
  </label><br>

  <label>
    <input type="checkbox" name="alertas" value="true"
      %%[ IF @pAlertas == "true" THEN ]%% checked %%[ ENDIF ]%%>
    Alertas e Notificações
  </label><br>

  <button type="submit">Salvar Preferências</button>
  <a href="%%=CloudPagesURL(124)=%%">Desinscrever de Tudo</a>
</form>
</body>
</html>
```

---

## Handler de Formulário — AMPscript

```ampscript
%%[
/* ===== HANDLER — SALVAR PREFERÊNCIAS ===== */

SET @subKey      = RequestParameter("subKey")
SET @email       = RequestParameter("email")
SET @newsletter  = IIF(RequestParameter("newsletter") == "true", "true", "false")
SET @promocoes   = IIF(RequestParameter("promocoes") == "true", "true", "false")
SET @transacional= IIF(RequestParameter("transacional") == "true", "true", "false")
SET @alertas     = IIF(RequestParameter("alertas") == "true", "true", "false")

/* Validação básica */
IF Empty(@subKey) OR Empty(@email) THEN
  SET @erro = "Dados inválidos. Por favor, acesse o link do seu e-mail."
ELSE
  /* Upsert na DE de preferências */
  SET @result = UpsertData(
    "DE_EmailPreferences",
    1, "SubscriberKey", @subKey,
    "Email",        @email,
    "Newsletter",   @newsletter,
    "Promocoes",    @promocoes,
    "Transacional", @transacional,
    "Alertas",      @alertas,
    "UpdateDate",   Now()
  )

  /* Se desmarcou tudo, desinscrever globalmente */
  IF @newsletter == "false" AND @promocoes == "false" AND @transacional == "false" AND @alertas == "false" THEN
    SET @unsubResult = InvokeDelete("Subscriber", 1, "SubscriberKey", @subKey, "EmailAddress", @email)
  ENDIF

  SET @sucesso = "true"
ENDIF
]%%

%%[ IF NOT Empty(@erro) THEN ]%%
  <p style="color:red;">%%=v(@erro)=%%</p>
%%[ ELSEIF @sucesso == "true" THEN ]%%
  <p>Preferências salvas com sucesso! ✅</p>
%%[ ENDIF ]%%
```

---

## Handler de Formulário — SSJS

```javascript
<script runat="server">
Platform.Load("Core", "1");

var subKey       = Platform.Request.GetFormField("subKey");
var email        = Platform.Request.GetFormField("email");
var newsletter   = Platform.Request.GetFormField("newsletter") === "true";
var promocoes    = Platform.Request.GetFormField("promocoes") === "true";
var transacional = Platform.Request.GetFormField("transacional") === "true";
var alertas      = Platform.Request.GetFormField("alertas") === "true";

if (!subKey || !email) {
  Variable.SetValue("@erro", "Dados inválidos.");
} else {
  try {
    // Upsert preferences DE
    var de = DataExtension.Init("DE_EmailPreferences");
    de.Rows.Add({
      SubscriberKey: subKey,
      Email: email,
      Newsletter: newsletter,
      Promocoes: promocoes,
      Transacional: transacional,
      Alertas: alertas,
      UpdateDate: Platform.Function.SystemDateToLocalDate(new Date())
    });
    
    // Se tudo desmarcado → unsubscribe global
    if (!newsletter && !promocoes && !transacional && !alertas) {
      var api = new Script.Util.WSProxy();
      var subObj = {
        EmailAddress: email,
        SubscriberKey: subKey,
        Status: "Unsubscribed"
      };
      api.update("Subscriber", subObj, null, null);
    }
    
    Variable.SetValue("@sucesso", "true");
  } catch(e) {
    Variable.SetValue("@erro", e.message);
  }
}
</script>
```

---

## DE de Preferências — Estrutura

```sql
/* DE_EmailPreferences */
Nome: DE_EmailPreferences
Tipo: Standard DE
Campos:
  SubscriberKey  Text(36)   PK  Obrigatório
  Email          Email       -   Obrigatório
  Newsletter     Boolean     -   Default: true
  Promocoes      Boolean     -   Default: true
  Transacional   Boolean     -   Default: true
  Alertas        Boolean     -   Default: true
  UpdateDate     Date        -
  Source         Text(50)    -
```

---

## Link no E-mail para Preference Center

```ampscript
%%[
/* Gerar link com dados do subscriber embutidos */
SET @prefURL = CloudPagesURL(
  123,
  "sub", _subscriberkey,
  "email", emailaddr,
  "job", _JobID
)
]%%

<a href="%%=v(@prefURL)=%%">Gerenciar Preferências</a>
```

---

## Usar Preferências no Journey Builder

```
Journey Entry: Todos os Ativos

Decisão: Verificar Newsletter = true?
  ├── SIM → Send Email (Newsletter)
  └── NÃO → Skip / Exit
  
Decisão: Verificar Promocoes = true?
  ├── SIM → Send Email (Oferta)
  └── NÃO → Skip / Exit
```

### SQL para Segment baseado em Preferências

```sql
-- Assinantes que aceitam Newsletter mas não Promoções
SELECT
  p.SubscriberKey,
  p.Email,
  p.Newsletter,
  p.Promocoes
FROM DE_EmailPreferences p
JOIN _Subscribers s ON p.SubscriberKey = s.SubscriberKey
WHERE p.Newsletter = 1
  AND p.Promocoes = 0
  AND s.Status = 'Active'
```

---

## Boas Práticas

```
✅ Sempre pré-popular o formulário com as preferências atuais
✅ Ofereça opção de "pausa" (30/60 dias) antes do unsubscribe total
✅ Use CloudPagesURL() com parâmetros para rastrear a origem
✅ Confirme as mudanças por e-mail transacional
✅ Mantenha log de todas as mudanças (data, IP, origem)
✅ Sincronize preferências com o CRM (Sales Cloud) via API
✅ Teste o fluxo completo com subscribers reais em ambiente de sandbox
✅ Garanta que a página funcione sem JavaScript (acessibilidade)
```

---

## Referências

- [CloudPages Docs](https://help.salesforce.com/s/articleView?id=sf.mc_cp_cloud_pages.htm)
- [Profile Center Customization](https://help.salesforce.com/s/articleView?id=sf.mc_es_profile_center.htm)
- [UpsertData AMPscript Function](https://ampscript.guide/upsertdata/)

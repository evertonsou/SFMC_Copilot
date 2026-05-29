# Module: Salesforce Integration

> **Skill**: sfmc-copilot | **Categoria**: CRM Integration | **Tokens**: ~3K

---

## Visão Geral

A integração entre **Salesforce Marketing Cloud** e **Sales Cloud (CRM)** permite sincronizar dados de contatos, leads e oportunidades para personalizar comunicações e acionar jornadas baseadas em eventos CRM.

---

## Tipos de Integração

| Tipo | Descrição | Quando Usar |
|------|-----------|-------------|
| **Marketing Cloud Connect** | Conector nativo Salesforce ↔ SFMC | Integração padrão, mais simples |
| **Synchronized Data Extensions** | Espelha objetos Salesforce como DEs | Acesso rápido a dados CRM em emails |
| **Salesforce Entry Events** | Journeys disparadas por mudanças no CRM | Automação baseada em eventos |
| **REST API** | Integração programática | Cenários customizados |

---

## Marketing Cloud Connect

### Configuração Inicial

1. **Setup** (SFMC) → **Integrated Apps** → **Salesforce Integration**
2. Conectar org Salesforce com credenciais de admin
3. Configurar mapeamento de Business Unit ↔ Org
4. Ativar sincronização

### Objetos Sincronizáveis

| Objeto CRM | Equivalente SFMC |
|-----------|------------------|
| `Contact` | Subscriber / Contact |
| `Lead` | Subscriber / Contact |
| `Campaign` | Salesforce Campaign |
| `Campaign Member` | List Member |
| `Opportunity` | (via Synchronized DE) |
| `Account` | (via Synchronized DE) |
| Custom Objects | (via Synchronized DE) |

---

## Synchronized Data Extensions (SDEs)

### O que são
SDEs são **réplicas de objetos Salesforce** dentro do SFMC. Sincronizam automaticamente em intervalos configuráveis (15 min, 1h, 6h, 24h).

### Criar uma SDE

1. **Contact Builder** → **Data Sources** → **Salesforce Objects**
2. Selecionar o objeto (Contact, Lead, Custom)
3. Escolher campos para sincronizar
4. Definir frequência de sincronização
5. Ativar

### Campos Recomendados para Sincronizar

#### Objeto: Contact
```
Id                    → ContactID (PK)
Email                 → EmailAddress
FirstName             → FirstName
LastName              → LastName
Phone                 → Phone
AccountId             → AccountID
MailingState          → State
MailingCountry        → Country
LeadSource            → LeadSource
RecordType            → RecordType
LastModifiedDate      → LastModified
```

#### Objeto: Lead
```
Id                    → LeadID (PK)
Email                 → EmailAddress
FirstName             → FirstName
LastName              → LastName
Company               → Company
Status                → LeadStatus
IsConverted           → IsConverted
ConvertedDate         → ConvertedDate
LeadSource            → LeadSource
```

---

## Usar Dados CRM em E-mails (AMPscript)

### Lookup em SDE de Contato

```ampscript
%%[
/* Buscar dados do contato na SDE sincronizada do CRM */
SET @subKey = _subscriberkey

SET @row = LookupRows("Contact_Salesforce", "Id", @subKey)

IF RowCount(@row) > 0 THEN
  SET @r = Row(@row, 1)
  SET @nome        = Field(@r, "FirstName")
  SET @empresa     = Field(@r, "AccountName")
  SET @estado      = Field(@r, "MailingState")
  SET @segmento    = Field(@r, "Segment__c")
ENDIF
]%%

Olá, %%=v(@nome)=%%!

Temos uma oferta especial para empresas de %%=v(@estado)=%% como a %%=v(@empresa)=%%.
```

### Lookup em Objeto Personalizado CRM

```ampscript
%%[
/* Buscar último contrato do cliente na SDE de Opportunity */
SET @oppRows = LookupRows("Opportunity_Salesforce", "AccountId", @accountID)
SET @latestAmount = 0

FOR @i = 1 TO RowCount(@oppRows) DO
  SET @opp = Row(@oppRows, @i)
  SET @amount = Field(@opp, "Amount")
  IF @amount > @latestAmount THEN
    SET @latestAmount = @amount
    SET @oppName = Field(@opp, "Name")
    SET @closeDate = Field(@opp, "CloseDate")
  ENDIF
NEXT @i
]%%

Seu contrato mais recente: %%=v(@oppName)=%%
Valor: R$ %%=FormatNumber(@latestAmount, "N2", "pt-BR")=%%
Data: %%=Format(@closeDate, "dd/MM/yyyy")=%%
```

---

## Salesforce Entry Events (Journey Builder)

### O que são
Eventos que **disparam jornadas** automaticamente quando algo muda no Salesforce CRM.

### Tipos de Entry Event

| Evento | Trigger |
|--------|---------|
| **Salesforce Data Event** | Campo de objeto muda (ex: Status Lead = "Qualified") |
| **Campaign Member Event** | Contato adicionado/removido de Campaign CRM |
| **API Event** | Disparo via REST API |

### Configurar Salesforce Data Event

1. Journey Builder → **New Journey** → **Entry Source: Salesforce Data**
2. Selecionar objeto (Contact, Lead, Custom)
3. Definir filtro: `Status = "Customer"` ou `Stage = "Closed Won"`
4. Configurar campos para injetar no Journey
5. Ativar

### Exemplo: Jornada de Onboarding pós-venda

```
Trigger: Opportunity.StageName mudou para "Closed Won"
    ↓
Wait: 1 hora
    ↓
Send Email: "Bem-vindo, {{FirstName}}!"
    ↓
Wait: 3 dias
    ↓
Decision Split: Abriu o e-mail?
    ├── SIM → Send Email: "Recursos Premium"
    └── NÃO → Send Email: "Lembrete de Boas-Vindas"
```

---

## SSJS — Buscar Dados do Salesforce via API

```javascript
<script runat="server">
Platform.Load("Core", "1");

// Autenticar com Salesforce (via Connected App)
function getSalesforceToken(clientId, clientSecret, sfInstanceUrl) {
  var req = new Script.Util.HttpRequest(sfInstanceUrl + "/services/oauth2/token");
  req.method = "POST";
  req.postData = "grant_type=client_credentials" +
                 "&client_id=" + clientId +
                 "&client_secret=" + clientSecret;
  var response = req.send();
  var parsed = Platform.Function.ParseJSON(response.content);
  return parsed.access_token;
}

// Query SOQL via REST API
function querySalesforce(token, sfInstanceUrl, soql) {
  var encodedSOQL = Platform.Function.Base64Encode(soql);
  var req = new Script.Util.HttpRequest(
    sfInstanceUrl + "/services/data/v57.0/query?q=" + soql
  );
  req.method = "GET";
  req.setHeader("Authorization", "Bearer " + token);
  req.setHeader("Content-Type", "application/json");
  var response = req.send();
  return Platform.Function.ParseJSON(response.content);
}

// Uso
var token = getSalesforceToken(
  "YOUR_CLIENT_ID",
  "YOUR_CLIENT_SECRET",
  "https://yourorg.salesforce.com"
);

var result = querySalesforce(
  token,
  "https://yourorg.salesforce.com",
  "SELECT Id, Name, Email FROM Contact WHERE Status__c = 'VIP' LIMIT 100"
);

Write(Stringify(result));
</script>
```

---

## SQL — Queries com SDEs

### Clientes CRM ativos sem engajamento de e-mail

```sql
SELECT
  c.Id          AS ContactID,
  c.Email       AS EmailAddress,
  c.FirstName,
  c.LastName,
  c.Account     AS Company,
  MAX(o.EventDate) AS LastOpen
FROM Contact_Salesforce c
LEFT JOIN _Open o ON c.Email = o.EmailAddress
  AND o.EventDate >= DATEADD(DAY, -180, GETDATE())
WHERE c.Status__c = 'Customer'
  AND c.IsActive__c = 1
GROUP BY c.Id, c.Email, c.FirstName, c.LastName, c.Account
HAVING MAX(o.EventDate) IS NULL
ORDER BY c.LastName
```

### Leads qualificados para nurturing

```sql
SELECT
  l.Id,
  l.Email,
  l.FirstName,
  l.LastName,
  l.Company,
  l.LeadSource,
  l.Status,
  l.CreatedDate
FROM Lead_Salesforce l
LEFT JOIN _Sent s ON l.Email = s.EmailAddress
WHERE l.Status = 'Working'
  AND l.IsConverted = 0
  AND s.EmailAddress IS NULL  -- Nunca recebeu e-mail
ORDER BY l.CreatedDate DESC
```

---

## Campaign Member — Gerenciar via AMPscript

```ampscript
%%[
/* Adicionar subscriber à Salesforce Campaign após clique */
SET @campaignID = "701xx000001ABCDEF"
SET @contactID  = Lookup("Contact_Salesforce", "Id", "Email", emailaddr)

IF NOT Empty(@contactID) THEN
  /* Atualizar status na DE de Campaign Members */
  SET @result = UpsertData(
    "CampaignMember_Salesforce",
    2,
    "CampaignId", @campaignID,
    "ContactId",  @contactID,
    "Status",     "Responded",
    "HasResponded", "true"
  )
ENDIF
]%%
```

---

## Boas Práticas

```
✅ Sincronize apenas os campos necessários — evite DEs gigantes
✅ Use SDEs para leitura, API para escrita (alterações no CRM)
✅ Monitore erros de sincronização em Contact Builder → Sync Log
✅ Defina um campo de opt-in no CRM para controlar envios
✅ Documente o mapeamento de campos CRM ↔ SFMC
✅ Teste Salesforce Entry Events com registros de sandbox
✅ Considere latência de sincronização (até 15 min) ao desenhar jornadas
✅ Configure filtros nas SDEs para não sincronizar registros desnecessários
```

---

## Referências

- [Marketing Cloud Connect](https://help.salesforce.com/s/articleView?id=sf.mc_co_marketing_cloud_connect.htm)
- [Synchronized DEs](https://help.salesforce.com/s/articleView?id=sf.mc_co_synchronized_data_extensions.htm)
- [Salesforce Entry Events](https://help.salesforce.com/s/articleView?id=sf.mc_jb_salesforce_entry_event.htm)
- [SFMC REST API](https://developer.salesforce.com/docs/marketing/marketing-cloud/references)

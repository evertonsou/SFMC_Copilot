---
name: sfmc-journey-builder
description: Journey Builder configuration, activities, entry sources, and patterns.
---

# SFMC Journey Builder Module

Construção e gerenciamento de jornadas de cliente.

## Activity Types

| Type | Description |
|------|-------------|
| Entry Event | Inicia a jornada |
| Email | Envia email |
| Decision | Branch baseado em condições |
| Wait | Pausa/Delay |
| Action | SMS, Push, Salesforce, etc. |
| Engagement | Split based on email interaction |

## Entry Sources

| Entry Type | Description |
|------------|-------------|
| API | Injeção via REST API |
| CloudPages | Form submission |
| Data Extension | Scheduled or triggered |
| Audience | List/Publication |
| Salesforce | SF events |

## Activities Reference

### Email Activity
```javascript
// Trigger via REST API
POST /interaction/v1/events
{
  "ContactKey": "contact@example.com",
  "EventDefinitionKey": "journey-entry-key",
  "Data": {
    "FirstName": "John"
  }
}
```

### Decision Split
```
IF [Email] CONTAINS "vip" THEN
  → VIP Path
ELSE IF [Engagement Score] > 50 THEN
  → High Engaged Path
ELSE
  → Standard Path
END
```

### Wait/Delay
- Duration: 1 hour, 2 days, 1 week
- Wait by: Date/Time attribute
- Specific date/time

### Action Activities
- **Salesforce**: Update CRM records
- **SMS**: Send text message
- **Push**: Send push notification
- **Custom**: Custom activity (developer)

## Journey Patterns

### Welcome Series
```
Entry (New Subscriber) → Email 1 (Welcome) → Wait 3 days → Email 2 (Product) → Wait 7 days → Email 3 (Offer)
```

### Abandoned Cart
```
Entry (Cart Abandoned) → Wait 1 hour → Email 1 (Reminder) → Decision (Opened?) 
  → Yes: Email 2 (Incentive) 
  → No: Wait 1 day → Email 1 (Reminder)
```

### Birthday Journey
```
Entry (Contact with Birthday today) → Email (Birthday offer) → Wait 1 week → SMS (Follow-up)
```

## REST API - Journeys

### Create Journey
```javascript
POST /interaction/v1/interactions
{
  "name": "My Journey",
  "key": "my-journey-key",
  "entrySource": {
    "objectTypeId": "api",
    "name": "API Entry"
  },
  "status": "Draft",
  "triggers": [
    {
      "key": "entry-key",
      "name": "Entry Event"
    }
  ]
}
```

### Get Journey
```javascript
GET /interaction/v1/interactions/key:{journeyKey}
```

### Update Journey
```javascript
PUT /interaction/v1/interactions/key:{journeyKey}
```

### Activate/Deactivate
```javascript
POST /interaction/v1/interactions/key:{journeyKey}/status
{ "status": "Published" }
```

## Versioning

- Journeys são versionados
- Edits criam nova versão draft
- Ative apenas uma versão por vez
- Versões antigas podem ser mantidas para histórico

---

## Planejamento e Solução de Jornadas (Checklist do Analista)

Para desenhar jornadas complexas de forma eficiente (ex: réguas de reengajamento unificadas), utilize o seguinte checklist:

### 1. Definição da Entrada (Entry Source)
- **Data Extension única:** Prefira uma única DE de entrada alimentada por queries SQL estruturadas para centralizar as campanhas de reaquecimento similares.
- **Filtros de Entrada:** Configure critérios claros para que leads desqualificados ou já convertidos não entrem na jornada.

### 2. Fluxo e Bifurcação (Decisions)
- **Decision Splits (Divisão de Decisão):** Use splits baseados em atributos (ex: `Origem = SOCIO5ESTRELAS`) para direcionar caminhos específicos dentro do mesmo fluxo, evitando criar jornadas duplicadas.
- **Dynamic Content:** Para economizar espaço no Journey Builder, utilize o mesmo bloco de envio com conteúdos e links dinâmicos configurados via AMPscript no template do e-mail.

### 3. Critérios de Saída e Metas (Exit Criteria & Goals)
- **Meta (Goal):** Defina a conversão como a meta da jornada (ex: `Status = Ativo` ou `Cliente = True`). Quando a meta é atingida, o contato sai da jornada imediatamente e é contabilizado na taxa de sucesso.
- **Exit Criteria:** Configure critérios de saída para remover contatos que mudaram de status (ex: lead que desqualificou ou cancelou a negociação durante o tempo de espera).

### 4. Integração CRM (Salesforce Activities)
- Use a atividade **Object Activity** para atualizar automaticamente registros no Salesforce CRM diretamente a partir do Journey Builder (ex: alterar status para `Desqualificado` no D+31).
- Garanta que as atividades de envio registrem as interações de e-mail e WhatsApp de volta no histórico de atividades do CRM.
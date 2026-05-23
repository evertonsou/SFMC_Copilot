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
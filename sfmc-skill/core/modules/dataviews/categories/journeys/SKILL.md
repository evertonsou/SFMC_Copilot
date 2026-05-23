---
name: sfmc-dataviews-journeys
description: Journey Builder System DataViews.
---

# DataViews - Journeys

## _Journey
Journeys do sistema.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| JourneyID | string | ID único do journey |
| JourneyName | string | Nome do journey |
| JourneyVersion | int | Versão |
| CreatedDate | datetime | Data de criação |
| ModifiedDate | datetime | Data de modificação |
| StartDate | datetime | Data de início |
| EndDate | datetime | Data de fim (se aplicável) |
| Status | string | Status (Published, Draft, etc.) |
| CreatedBy | string | ID do criador |

## _JourneyActivity
Activities dentro de journeys.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| JourneyActivityID | string | ID da activity |
| JourneyID | string | ID do journey |
| ActivityName | string | Nome da activity |
| ActivityType | string | Tipo (Email, Decision, Wait, etc.) |
| ActivityOrder | int | Ordem no journey |
| Channel | string | Canal (Email, SMS, Push) |

**ActivityTypes comuns:**
- Email
- DecisionPoint
- Wait
- Action
- Restriction
- EntryEvent

## _JourneyExecution
Execuções de journeys.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| ContactKey | string | Identificador do contato |
| JourneyID | string | ID do journey |
| JourneyActivityID | string | ID da activity atual |
| ExecutionDate | datetime | Data de entrada |
| ExitDate | datetime | Data de saída (se saiu) |
| Status | string | Active, Completed, Exited |

## _JourneyHistory
Histórico de contatos em journeys.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| ContactKey | string | Identificador |
| JourneyID | string | ID do journey |
| JourneyActivityID | string | Activity que ejecutou |
| EventDate | datetime | Data do evento |
| EventType | string | Tipo do evento |
| CampaignID | string | ID da campanha |

## Query de Journey Tracking

```sql
SELECT 
    j.JourneyName,
    j.Status,
    h.ContactKey,
    h.EventDate,
    h.EventType,
    ja.ActivityName,
    ja.ActivityType
FROM _Journey j
INNER JOIN _JourneyHistory h ON j.JourneyID = h.JourneyID
LEFT JOIN _JourneyActivity ja ON h.JourneyActivityID = ja.JourneyActivityID
WHERE h.ContactKey = '%%subscriberKey%%'
ORDER BY h.EventDate DESC
```
---
name: sfmc-dataviews
description: Complete reference for Salesforce Marketing Cloud System DataViews. Email, SMS, Push, Subscribers, Journeys, Automations, and more.
---

# SFMC DataViews Module

System DataViews são visões read-only dos dados do SFMC. Use-as para relatórios, segmentação e análises.

## Visão Geral

| Categoria | DataViews |
|-----------|-----------|
| Email | _Sent, _Open, _Click, _Bounce, _Unsubs, _Forward, _Survey |
| SMS | _SMSMessageTracking, _SMSSubscriptionLog, _SMSJob |
| Push | _PushAddress, _PushTag, _PushTrackLink, _PushAbuseReport |
| Subscribers | _Subscribers, _ListSubscribers, _Status |
| Journeys | _Journey, _JourneyActivity, _JourneyExecution |
| Automation | _Automation, _AutomationInstance |
| Content | _Content, _Asset, _Folder |
| Enterprise | ENT._Subscribers, ENT._EnterpriseAttribute |

## Carregamento de Sub-módulos

- `dataviews/email` - DataViews de email
- `dataviews/sms` - DataViews de SMS
- `dataviews/push` - DataViews de Push
- `dataviews/subscribers` - DataViews de subscribers
- `dataviews/journeys` - DataViews de Journey Builder
- `dataviews/automation` - DataViews de automações
- `dataviews/content` - DataViews de conteúdo
- `dataviews/enterprise` - DataViews Enterprise

## Queries Comuns

```sql
-- Ver todos os opens de um contato
SELECT SubscriberKey, EventDate, IsUnique
FROM _Open
WHERE SubscriberKey = 'contact@example.com'

-- Tracking completo
SELECT 
    s.SubscriberKey,
    s.EventDate as SendDate,
    o.EventDate as OpenDate,
    c.EventDate as ClickDate
FROM _Sent s
LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey AND s.JobID = o.JobID
LEFT JOIN _Click c ON s.SubscriberKey = c.SubscriberKey AND s.JobID = c.JobID
WHERE s.SubscriberKey = 'contact@example.com'
```
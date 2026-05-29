---
name: sfmc-dataviews-subscribers
description: Subscriber-related System DataViews in Salesforce Marketing Cloud.
---

# DataViews - Subscribers

## _Subscribers
Todos os subscribers do sistema.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador único |
| EmailAddress | string | Endereço de email |
| Status | string | Active, Held, Bounced, Unsubscribed |
| DateJoined | datetime | Data de criação |
| DateUnsubscribed | datetime | Data de unsubscribe |
| DateLastEmailed | datetime | Último email enviado |
| BounceCount | int | Contador de bounces |
| Language | string | Código do idioma |
| Locale | string | Localidade |
| MobileConnectStatus | string | Status SMS |

## _ListSubscribers
Subscriber por lista.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| ListID | int | ID da lista |
| Status | string | Status na lista |
| DateJoined | datetime | Data de entrada |
| DateUnsubscribed | datetime | Data de saída |

## _Status
Status detalhado de subscribers.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| Status | string | Status atual |
| StatusDate | datetime | Data do status |
| StatusCode | int | Código numérico |

## _BusinessUnit
Unidades de negócio (Enterprise 2.0).

| Campo | Tipo | Descrição |
|-------|------|-----------|
| BusinessUnitID | int | ID da unidade |
| BusinessUnitName | string | Nome da unidade |
| ParentBusinessUnitID | int | ID da unidade pai |

## Queries Úteis

```sql
-- Subscribers ativos
SELECT SubscriberKey, EmailAddress, Status
FROM _Subscribers
WHERE Status = 'Active'

-- Por lista
SELECT s.SubscriberKey, s.EmailAddress, l.Status, l.DateJoined
FROM _Subscribers s
INNER JOIN _ListSubscribers l ON s.SubscriberKey = l.SubscriberKey
WHERE l.ListID = 12345

-- Hards bounces recentes
SELECT SubscriberKey, EmailAddress, BounceCount
FROM _Subscribers
WHERE BounceCount > 0
ORDER BY BounceCount DESC
```
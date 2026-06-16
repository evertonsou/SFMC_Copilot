---
name: sfmc-dataviews-push
description: Push notification-related System DataViews in Salesforce Marketing Cloud.
---

# DataViews - Push

## _PushAddress
Endereços de dispositivo para push.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador do contato |
| DeviceID | string | ID do dispositivo |
| DeviceType | string | iOS, Android |
| PushAddressID | int | ID único do endereço |
| Status | string | Status (Active, Inactive) |
| OptedIn | datetime | Data de opt-in |
| OptedOut | datetime | Data de opt-out |

## _PushTag
Tags associated com devices.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| DeviceID | string | ID do dispositivo |
| TagName | string | Nome da tag |
| TagType | string | Tipo da tag |
| CreatedDate | datetime | Data de criação |

## _PushTrackLink
Tracking de cliques em links de push.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| PushAddressID | int | ID do endereço |
| JobID | int | ID do job |
| ListID | int | ID da lista |
| CampaignID | int | ID da campanha |
| EventDate | datetime | Data/hora |
| URL | string | URL clicada |
| LinkName | string | Nome do link |

## _PushAbuseReport
Reports de abuso.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| DeviceID | string | ID do dispositivo |
| ReportType | string | Tipo de report |
| ReportDate | datetime | Data do report |

## Query de Tracking Push

```sql
SELECT 
    a.SubscriberKey,
    a.DeviceType,
    a.Status,
    t.TagName,
    l.EventDate AS ClickDate,
    l.URL
FROM _PushAddress a
LEFT JOIN _PushTag t ON a.SubscriberKey = t.SubscriberKey AND a.DeviceID = t.DeviceID
LEFT JOIN _PushTrackLink l ON a.SubscriberKey = l.SubscriberKey
WHERE a.SubscriberKey = '%%subscriberKey%%'
```
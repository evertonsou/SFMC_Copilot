---
name: sfmc-dataviews-sms
description: SMS-related System DataViews in Salesforce Marketing Cloud.
---

# DataViews - SMS

## _SMSMessageTracking
Tracking de mensagens SMS enviadas.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador do contato |
| MobileNumber | string | Número de telefone |
| CampaignID | int | ID da campanha SMS |
| JobID | int | ID do job |
| ListID | int | ID da lista |
| BatchID | int | ID do batch |
| SubID | int | Subscription ID |
| EventDate | datetime | Data/hora do evento |
| EventType | string | Type of event (Sent, Delivered, etc.) |
|SMSID | string | ID da mensagem |
| Code | string | Short code usado |
| Status | string | Status do envio |
| StatusMessage | string | Mensagem de status |

**EventTypes:**
- Sent
- Delivered
- Failed
- Undelivered

## _SMSSubscriptionLog
Log de assinaturas SMS.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| MobileNumber | string | Número |
| Code | string | Short code |
| Keyword | string | Keyword que iniciou |
| CampaignID | int | ID da campanha |
| SubscriptionDate | datetime | Data da assinatura |
| UnsubscriptionDate | datetime | Data do unsubscribe (se aplicável) |
| Status | string | Active ou Unsubscribed |

## _SMSJob
Informações do job SMS.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| JobID | int | ID do job |
| CampaignID | int | ID da campanha |
| JobName | string | Nome do job |
| JobStatus | string | Status do job |
| CreatedDate | datetime | Data de criação |
| ModifiedDate | datetime | Data de modificação |
| JobType | string | Tipo (Push, SMS, etc.) |

## Query de Tracking SMS

```sql
SELECT 
    t.SubscriberKey,
    t.MobileNumber,
    t.EventDate,
    t.EventType,
    t.Status,
    s.Keyword,
    s.SubscriptionDate
FROM _SMSMessageTracking t
LEFT JOIN _SMSSubscriptionLog s ON t.SubscriberKey = s.SubscriberKey
WHERE t.MobileNumber = '5511999999999'
ORDER BY t.EventDate DESC
```
# Cemig SIM - Processo de Extração de Envios e Engajamento por Contato

Este documento descreve as diretrizes e consultas SQL utilizadas para mapear e extrair o histórico consolidado de disparos (envios) e engajamento (aberturas, cliques, recebimento e falhas) de um contato específico, cruzando dados de todos os canais ativos (E-mail, SMS, WhatsApp e MobilePush).

---

## 1. Visão Geral do Processo

A extração de dados funciona sob demanda a partir do identificador único do contato no Salesforce Marketing Cloud: **`SubscriberKey`** (ou **`ContactKey`**).

As buscas estão divididas entre:
- **Canais Nativos (E-mail e SMS):** Consultas diretas utilizando as tabelas do sistema (**System Data Views**).
- **Canais Customizados (WhatsApp e MobilePush):** Consultas contra as tabelas de tracking geral personalizadas do cliente (**Data Extensions de Tracking**), uma vez que não existem views nativas no SFMC para esses canais.

---

## 2. Canal: E-mail Marketing

O histórico de e-mails utiliza a estrutura nativa de Data Views da org. A tabela `_Subscribers` (ou `Ent._Subscribers`) é utilizada para trazer o endereço de e-mail atual do contato.

### Query SQL de E-mail
```sql
SELECT 
    s.JobID,
    s.SubscriberKey,
    sub.EmailAddress,
    j.EmailName,
    s.EventDate AS SentEventDate,
    o.EventDate AS OpenEventDate,
    c.EventDate AS ClickEventDate,
    un.EventDate AS UnsubscribeEventDate,
    comp.EventDate AS ComplaintEventDate
FROM 
    _Sent s
INNER JOIN 
    _Job j ON s.JobID = j.JobID
LEFT JOIN 
    _Subscribers sub ON s.SubscriberID = sub.SubscriberID
LEFT JOIN 
    _Open o ON s.JobID = o.JobID 
        AND s.ListID = o.ListID 
        AND s.BatchID = o.BatchID 
        AND s.SubscriberID = o.SubscriberID 
        AND o.IsUnique = 1
LEFT JOIN 
    _Click c ON s.JobID = c.JobID 
        AND s.ListID = c.ListID 
        AND s.BatchID = c.BatchID 
        AND s.SubscriberID = c.SubscriberID 
        AND c.IsUnique = 1
LEFT JOIN 
    _Unsubscribe un ON s.JobID = un.JobID 
        AND s.ListID = un.ListID 
        AND s.BatchID = un.BatchID 
        AND s.SubscriberID = un.SubscriberID
LEFT JOIN 
    _Complaint comp ON s.JobID = comp.JobID 
        AND s.ListID = comp.ListID 
        AND s.BatchID = comp.BatchID 
        AND s.SubscriberID = comp.SubscriberID
WHERE 
    s.SubscriberKey = '003bJ00000Ai8Z1QAJ'
```

---

## 3. Canal: SMS (MobileConnect)

A rastreabilidade de SMS utiliza a tabela nativa `_SMSMessageTracking`. O campo de data de envio é o `ActionDateTime` e o identificador do disparo individual é o `MobileMessageTrackingID`.

### Query SQL de SMS
```sql
SELECT 
    MobileMessageTrackingID,
    SubscriberKey,
    Mobile,
    Name,
    ActionDateTime,
    MessageText,
    Sent,
    Delivered,
    Outbound
FROM 
    _SMSMessageTracking
WHERE 
    SubscriberKey = '003bJ00000Ai8Z1QAJ'
    AND Outbound = 1
```

---

## 4. Canal: WhatsApp

Como não existem Data Views nativas de WhatsApp no SFMC, a Cemig SIM utiliza a Data Extension consolidada de tracking `DEX_WHATSAPP_TRACKING_DELIVERY`, populada via atividade `ChatMessagingDetailExtract` no Automation Studio. O identificador do disparo é o `SendIdentifier`.

### Query SQL de WhatsApp
```sql
SELECT 
    SendIdentifier,
    ContactKey,
    MobileNumber,
    ActivityName,
    EventDateUTC,
    Status,
    Reason,
    ConversationType
FROM 
    DEX_WHATSAPP_TRACKING_DELIVERY
WHERE 
    ContactKey = '003bJ00000sjTKUQA2'
```

---

## 5. Canal: MobilePush

Assim como o WhatsApp, o MobilePush não possui Data Views de tracking nativas. O rastreamento consolidado é feito na Data Extension `DEX_PUSH_DETAIL`, alimentada pelo MobilePush Detail Extract via Automation Studio. O identificador do disparo é o `PushJobId`.

### Query SQL de MobilePush
```sql
SELECT 
    PushJobId,
    ContactKey,
    DeviceId,
    MessageName,
    DateTimeSend,
    OpenDate,
    Status
FROM 
    DEX_PUSH_DETAIL
WHERE 
    ContactKey = '003bJ00000Ai8Z1QAJ'
```

---

## 6. Instruções de Execução

1. **Ferramenta Recomendada:** Utilize o **Query Studio** para buscas rápidas ad-hoc de teste.
2. **Automações de Auditoria:** Caso precise consolidar essas informações para uma base de auditoria em produção, configure as queries no **Automation Studio** direcionando as saídas com a ação `Append` para uma Data Extension de histórico de contatos customizada.
3. **Child Business Units (BUs):** Se rodar a query de E-mail a partir de uma Business Unit filha e receber um erro referente à tabela `_Subscribers`, adicione o prefixo `Ent.` (ficando `Ent._Subscribers`) para buscar a tabela diretamente na BU Pai.

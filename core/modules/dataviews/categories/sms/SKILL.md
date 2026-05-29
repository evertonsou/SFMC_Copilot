---
name: sfmc-dataviews-sms
description: SMS-related System DataViews in Salesforce Marketing Cloud.
---

# DataViews - SMS

## _SMSMessageTracking
Esta Data View contém o histórico completo de envio e recebimento de mensagens de SMS do MobileConnect. Ela registra tanto mensagens enviadas (outbound) quanto recebidas (inbound).

| Campo | Tipo | Descrição |
| :--- | :---: | :--- |
| **MobileMessageTrackingID** | string | Identificador exclusivo do registro de tracking |
| **EID** | int | ID da conta Enterprise no SFMC |
| **MID** | int | ID do Member/Business Unit no SFMC |
| **Mobile** | string | Número de telefone celular do contato |
| **MessageID** | string | ID exclusivo da mensagem de SMS |
| **KeywordID** | string | ID exclusivo da palavra-chave utilizada |
| **CodeID** | string | ID exclusivo do código de SMS (short/long code) |
| **ConversationID** | string | ID exclusivo da conversa SMS (se criada por AMPscript) |
| **ConversationStateID** | string | Correlaciona mensagens de ida (MT) e volta (MO) |
| **CampaignID** | string | ID da campanha de disparo (se enviado via DE/Campaign) |
| **Sent** | bool | Indica se o SMS foi enviado com sucesso (0 = Não, 1 = Sim) |
| **Delivered** | bool | Indica se o SMS foi entregue com sucesso (0 = Não, 1 = Sim) |
| **Undelivered** | bool | Indica se a entrega falhou (0 = Não, 1 = Sim) |
| **Outbound** | bool | Indica se a mensagem foi de saída/envio (0 = Não, 1 = Sim) |
| **Inbound** | bool | Indica se a mensagem foi de entrada/resposta (0 = Não, 1 = Sim) |
| **Name** | string | Nome da mensagem de SMS (corresponde à atividade do Journey Builder) |
| **MessageText** | string | O texto real da mensagem de SMS enviado |
| **CreateDateTime** | datetime | Data e hora de criação do registro (CST) |
| **ModifiedDateTime** | datetime | Data e hora da última modificação (CST) |
| **ActionDateTime** | datetime | Data e hora em que a ação/disparo ocorreu (CST) |
| **IsValid** | bool | Validade da mensagem (geralmente 1 para entrada e 0 para saída) |
| **SendID** | string | ID do envio/job (populado quando enviado via automação/DE) |

## _SMSSubscriptionLog
Esta Data View é a tabela oficial suportada para rastrear o histórico do status de assinatura de SMS dos contatos no MobileConnect. Substitui a antiga e não suportada view `_MobileSubscription`.

| Campo | Tipo | Descrição |
| :--- | :---: | :--- |
| **LogDate** | datetime | Data e hora de gravação da alteração de status (CST) |
| **SubscriberKey** | string | Chave de contato exclusiva no SFMC |
| **MobileSubscriptionID** | string | Identificador do registro de assinatura móvel |
| **SubscriptionDefinitionID** | string | Identificador da definição da assinatura (associação de keyword/short code) |
| **MobileNumber** | string | Número de telefone celular |
| **OptOutStatusID** | int | ID numérico do status de Opt-Out |
| **OptOutMethodID** | int | ID do método pelo qual ocorreu o Opt-Out |
| **OptOutDate** | datetime | Data e hora de ocorrência do Opt-Out (CST) |
| **OptInStatusID** | int | ID numérico do status de Opt-In |
| **OptInMethodID** | int | ID do método pelo qual ocorreu o Opt-In |
| **OptInDate** | datetime | Data e hora de ocorrência do Opt-In (CST) |
| **Source** | string | Canal ou origem da importação/registro da assinatura |
| **CreatedDate** | datetime | Data e hora de criação do registro (CST) |
| **ModifiedDate** | datetime | Data e hora da última modificação do registro (CST) |

> [!WARNING]
> **Tabela Inexistente:** Não existe uma Data View padrão suportada no Salesforce Marketing Cloud chamada `_SMSJob`. Detalhes sobre mensagens enviadas devem ser obtidos diretamente na `_SMSMessageTracking`.

## Query de Tracking SMS de Contato
Abaixo está um exemplo de query funcional para obter o histórico de envios e entregas de um contato filtrando pela sua `SubscriberKey`:

```sql
SELECT 
    SubscriberKey,
    Mobile,
    Name AS Nome_do_SMS,
    ActionDateTime AS Data_Envio_UTC,
    MessageText AS Texto_Mensagem,
    CASE 
        WHEN Sent = 1 AND Delivered = 1 THEN '📩 Entregue'
        WHEN Sent = 1 AND Delivered = 0 THEN '📤 Enviado (Aguardando Entrega)'
        WHEN Sent = 0 THEN '❌ Falha no Envio'
        ELSE 'Desconhecido'
    END AS Status_SMS
FROM 
    _SMSMessageTracking
WHERE 
    SubscriberKey = '003bJ00000Ai8Z1QAJ'
    AND Outbound = 1
```
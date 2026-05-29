---
name: sfmc-dataviews-push
description: Push notification-related System DataViews in Salesforce Marketing Cloud.
---

# DataViews - Push

No Salesforce Marketing Cloud, **não existem Data Views de sistema (tabelas nativas)** para consultar dados de disparos/envios (`_Sent`), aberturas (`_Open`) ou cliques/engajamento (`_Click`) do canal **MobilePush**.

As únicas duas Data Views nativas de Push disponíveis para consulta via SQL no SFMC são destinadas exclusivamente ao **cadastro e configuração dos aparelhos**:

1. **`_PushAddress`**: Armazena os dados de registro dos dispositivos móveis que possuem o aplicativo instalado.
2. **`_PushTag`**: Armazena as tags associadas a cada dispositivo registrado.

---

## 1. Tabela: _PushAddress
Esta Data View contém as informações de registro de dispositivos (celulares) para envio de Push Notifications via MobilePush. Como ela não é documentada oficialmente, seus campos possuem a particularidade de iniciarem com um sublinhado (`_`).

| Campo | Tipo | Descrição |
| :--- | :---: | :--- |
| **_ContactID** | string | Corresponde ao ContactKey / SubscriberKey do contato |
| **_DeviceID** | string | ID exclusivo do dispositivo móvel |
| **_APID** | string | ID do aplicativo cadastrado no MobilePush |
| **_Status** | string | Status do dispositivo (Active, Inactive) |
| **_Platform** | string | Sistema operacional (iOS, Android) |
| **_SystemToken** | string | Token de push fornecido pelo sistema operacional (APNS/FCM) |
| **_OptInDate** | datetime | Data e hora em que o contato habilitou as notificações (CST) |
| **_OptOutDate** | datetime | Data e hora em que o contato desabilitou as notificações (CST) |
| **_CreatedDate** | datetime | Data de criação do registro no SFMC (CST) |
| **_ModifiedDate** | datetime | Data da última alteração no registro (CST) |

---

## 2. Tabela: _PushTag
Esta Data View armazena as tags (etiquetas de segmentação) que foram associadas a cada dispositivo registrado.

| Campo | Tipo | Descrição |
| :--- | :---: | :--- |
| **_DeviceID** | string | ID exclusivo do dispositivo móvel |
| **_APID** | string | ID do aplicativo cadastrado no MobilePush |
| **_Value** | string | O valor/nome da tag associada |
| **_CreatedDate** | datetime | Data de criação da tag (CST) |
| **_ModifiedDate** | datetime | Data da última alteração da tag (CST) |

---

## 3. Como Obter Dados de Envio e Engajamento de Push (Oficial)
Como não existem Data Views nativas para tracking de Push, a Salesforce recomenda obter esses dados através de:

1. **MobilePush Detail Extract (Recomendado para SQL)**:
   - Configurar uma atividade de **Data Extract** com o tipo **MobilePush Detail Extract** no Automation Studio.
   - Usar um **File Transfer** para mover o arquivo ZIP extraído do Safehouse para o FTP do SFMC.
   - Configurar uma atividade de **Import** para ler o arquivo do FTP e carregar os dados em uma Data Extension customizada (ex: `DEX_PUSH_TRACKING_LOG`).
   - Consultar essa Data Extension customizada via SQL para obter o `JobID`, `MessageName`, `SentDate` e `Status`.

2. **Push Message Detail Report**:
   - Executar ou agendar o relatório detalhado em **Analytics Builder > Reports**.

3. **Dashboards de Monitoramento**:
   - Acessar o **Push Engagement Dashboard** via **Intelligence Reports for Engagement** (antigo Datorama).

---

## Query de Cadastro de Dispositivos (Push)
Abaixo está o único exemplo funcional de consulta à Data View nativa para listar os aparelhos de um contato específico:

```sql
SELECT 
    _ContactID AS ID_Contato,
    _DeviceID AS ID_Dispositivo,
    _APID AS ID_Aplicativo,
    _Platform AS Plataforma,
    _Status AS Status_Dispositivo,
    _OptInDate AS Data_OptIn,
    _OptOutDate AS Data_OptOut
FROM 
    _PushAddress
WHERE 
    _ContactID = '003bJ00000Ai8Z1QAJ'
```
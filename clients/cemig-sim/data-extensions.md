# Cemig SIM - Padrões de Data Extensions (DE)

Este documento centraliza as definições de campos, tipos de dados e padrões de nomenclatura das Data Extensions (DE) utilizadas nos projetos da Cemig SIM.

---

## 1. Campos Padrões de Operação e Disparo (E-mail Marketing)

Em todas as comunicações de E-mail Marketing da Cemig SIM, os seguintes campos são mapeados e utilizados como padrão na Data Extension de disparo:
- **NomeCompleto**: Campo contendo o nome completo do Lead, utilizado para personalizações de saudação (ex: `Olá, %%NomeCompleto%%!`). Deve possuir tratamento de fallback para quando o valor estiver em branco.
- **LinkAssinatura**: Campo dinâmico contendo o link individual e exclusivo do ambiente de assinatura do Salesforce CRM para redirecionamento do CTA principal.

---

## 2. Convenção de Nomenclatura de Campos

Na Cemig SIM, a nomenclatura dos campos nas Data Extensions de Envio (DEX) segue uma regra híbrida:

- **Campos de Controle e Personalização Amigáveis**: Campos que serão utilizados diretamente no AMPscript para personalização nos e-mails, chaves de controle ou links recebem nomes amigáveis (ex: `SubscriberKey`, `EntryDate`, `NomeCompleto`, `Telefone`, `LinkAssinatura`).
- **Campos Literais do CRM**: Campos com dados de atributos específicos do negócio que vêm direto do Salesforce CRM mantêm sua nomenclatura técnica literal com o sufixo `__c` (ex: `Desconto__c`, `CodigoAssociacao__c`, `TipoMercado__c`).

---

## 3. Tabelas de Stage do CRM (Salesforce Integration)

Como as tabelas sincronizadas do CRM (via Marketing Cloud Connect) possuem estruturas e nomenclaturas que variam por cliente, armazenamos a estrutura de stage offline para consulta rápida e prevenção de redundâncias em prompts.

### 3.1 Tabela: Lead_Salesforce

- **Origem:** Objeto `Lead` do Salesforce CRM (sincronizado automaticamente).
- **Última Data de Atualização Offline:** 2026-05-26

#### Estrutura de Campos (Lead_Salesforce)

| Campo                                 | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição                                |
| :------------------------------------ | :----------- | :----------------- | :-------------: | :--------------------------------------- |
| **\_ContactKey**                      | Text         | 254                |     **Sim**     | Chave do Contato no SFMC                 |
| **Id**                                | Text         | 18                 |     **Sim**     | Id do Lead no Salesforce CRM             |
| **Name**                              | Text         | 121                |       Não       | Nome completo do Lead                    |
| **FirstName**                         | Text         | 40                 |       Não       | Primeiro nome                            |
| **Email**                             | EmailAddress |                    |       Não       | E-mail do Lead                           |
| **MobilePhone**                       | Phone        |                    |       Não       | Telefone celular padrão                  |
| **Phone**                             | Phone        |                    |       Não       | Telefone fixo padrão                     |
| **TelefoneFormatadoWhats\_\_c**       | Text         | 1300               |       Não       | Celular formatado para WhatsApp          |
| **UsuarioDoMessaging\_\_c**           | Text         | 18                 |       Não       | Chave do usuário de mensageria           |
| **HashAutenticacao\_\_c**             | Text         | 255                |       Não       | Hash de autenticação                     |
| **ConsumoMedio\_\_c**                 | Decimal      |                    |       Não       | Consumo de energia informado             |
| **CPF\_\_c**                          | Text         | 14                 |       Não       | CPF do lead                              |
| **PlanoDesejado\_\_c**                | Text         | 18                 |       Não       | Código do plano                          |
| **CodigoAssociacao\_\_c**             | Text         | 40                 |       Não       | Código da associação/programa            |
| **LinkParaAmbienteDeAssinatura\_\_c** | Text         | 1300               |       Não       | Link individual para assinatura          |
| **Status**                            | Text         | 255                |       Não       | Status do lead no CRM                    |
| **Mediador\_\_c**                     | Text         | 255                |       Não       | Canal ou parceiro de prospecção          |
| **TipoMercado\_\_c**                  | Text         | 1300               |       Não       | Tipo de segmento (Residencial/Comercial) |
| **City\_\_c**                         | Text         | 18                 |       Não       | Cidade cadastrada                        |
| **CreatedDate**                       | Date         |                    |       Não       | Data de criação no CRM                   |
| **LastModifiedDate**                  | Date         |                    |       Não       | Data da última alteração no CRM          |
| **CreatedById**                       | Text         | 18                 |       Não       | Criador do registro                      |
| **LastModifiedById**                  | Text         | 18                 |       Não       | Modificador do registro                  |
| **OwnerId**                           | Text         | 18                 |       Não       | Proprietário do registro                 |
| **ParceiroAssociacao\_\_c**           | Text         | 18                 |       Não       | Código de parceiro associado             |
| **PartnerAccountId**                  | Text         | 18                 |       Não       | Conta parceira                           |
| **IndividualId**                      | Text         | 18                 |       Não       | ID individual                            |
| **MasterRecordId**                    | Text         | 18                 |       Não       | ID do registro master (mesclas)          |
| **RecordTypeId**                      | Text         | 18                 |       Não       | ID do Tipo de Registro no CRM            |
| **ConvertedAccountId**                | Text         | 18                 |       Não       | Conta gerada na conversão                |
| **ConvertedContactId**                | Text         | 18                 |       Não       | Contato gerado na conversão              |
| **ConvertedOpportunityId**            | Text         | 18                 |       Não       | Oportunidade gerada na conversão         |
| **HasOptedOutOfEmail**                | Boolean      |                    |       Não       | Flag de descadastro de e-mail            |
| **Indicado_por\_\_c**                 | Text         | 18                 |       Não       | Lead ou parceiro que indicou             |
| **Id_lead\_\_c**                      | Text         | 1300               |       Não       | Identificador extra de lead              |
| **Codigo_indicacao\_\_c**             | Text         | 15                 |       Não       | Código de indicação: `Codigo_indicacao__c` (Text 15)

### 3.2 Tabela: Plano__c_Salesforce

- **Origem:** Objeto customizado `Plano__c` do Salesforce CRM (sincronizado automaticamente).
- **Última Data de Atualização Offline:** 2026-05-26

#### Estrutura de Campos (Plano__c_Salesforce)

| Campo | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| **Id** | Text | 18 | **Sim** | Id do Plano no Salesforce CRM |
| **Name** | Text | 80 | Não | Nome comercial do plano |
| **Desconto__c** | Decimal | | Não | Percentual de desconto padrão associado ao plano |
| **RecordTypeId** | Text | 18 | Não | ID do Tipo de Registro no CRM |
| **OwnerId** | Text | 18 | Não | Proprietário do registro |
| **CreatedById** | Text | 18 | Não | ID do criador do plano |
| **LastModifiedById** | Text | 18 | Não | ID do último modificador do plano |

> [!IMPORTANT]
> **Controle de Atualização Offline:** Sempre que for requisitado o uso de um campo de CRM nas queries ou layouts que não conste na tabela acima, o Copilot deve **alertar o usuário e solicitar a estrutura atualizada da tabela de stage**, evitando suposições incorretas.

---

## 4. Tabelas de Tracking Customizadas por Canal (Sem Data Views Nativas)

### 4.1 Canal: WhatsApp

Esta seção agrupa as Data Extensions customizadas utilizadas para armazenar dados de envio e comportamento do canal WhatsApp da Cemig SIM.

#### Tabela: DEX_WHATSAPP_TRACKING_DELIVERY

- **Descrição:** Armazena o histórico completo e consolidado de envio e entrega de mensagens de WhatsApp da organização, seguindo o padrão de exportação `ChatMessagingDetailExtract`.
- **Origem:** Exportação offline via Automation Studio (Chat Messaging Extract).

##### Estrutura de Campos (DEX_WHATSAPP_TRACKING_DELIVERY)

| Campo | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| **PrimaryKey** | Text | 500 | **Sim** | Chave primária única gerada para o registro de rastreamento |
| **EventDateUTC** | Date | | Não | Data e hora em UTC do evento de rastreamento |
| **MobileNumber** | Text | 50 | Não | Número do celular formatado do contato |
| **EID** | Number | | Não | ID da conta Enterprise no SFMC |
| **SendIdentifier** | Text | 50 | Não | Identificador único do envio (Equivalente ao JobID para identificar o disparo) |
| **ConversationType** | Text | 50 | Não | Categoria de conversa do WhatsApp |
| **Reason** | Text | 400 | Não | Motivo do erro ou do status de falha, se aplicável |
| **ChannelType** | Text | 50 | Não | Tipo de canal (ex: WhatsApp) |
| **ActivityName** | Text | 400 | Não | Nome da atividade no Journey Builder |
| **JbDefinitionId** | Text | 50 | Não | ID da definição da jornada |
| **EntryDate** | Date | | Não | Data de inserção do registro |
| **ContactKey** | Text | 255 | Não | Chave do Contato (identificador único no SFMC) |
| **ChannelID** | Text | 50 | Não | Identificador do canal |
| **AssetId** | Text | 50 | Não | ID do conteúdo de mensagem do Content Builder |
| **MID** | Number | | Não | ID da Business Unit remetente |
| **Status** | Text | 50 | Não | Status de entrega (ex: Sent, Delivered, Read, Failed) |
| **JbActivityId** | Text | 50 | Não | ID de atividade da jornada no Journey Builder |
| **ChannelName** | Text | 50 | Não | Nome do canal de mensageria configurado |
| **SendType** | Text | 50 | Não | Tipo de envio executado |
| **AppID** | Text | 50 | Não | ID do aplicativo do canal |
| **MessageTypeID** | Text | 50 | Não | ID do tipo de mensagem enviado |
| **TrackingType** | Text | 50 | Não | Tipo de tracking registrado |

#### Tabela: DEX_WHATSAPP_POTENCIALUNSUBS

- **Descrição:** Registra contatos com potencial de descadastro (opt-out) ou bounces acumulados no canal de WhatsApp.
- **Origem:** Processamento analítico/automação interna da org.

##### Estrutura de Campos (DEX_WHATSAPP_POTENCIALUNSUBS)

| Campo | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| **PrimaryKey** | Text | 500 | **Sim** | Chave primária única do registro |
| **MobileNumber** | Phone | | Não | Número de telefone celular do contato |
| **MID** | Number | | Não | ID da Business Unit no SFMC |
| **LastDateUTC** | Date | | Não | Data e hora UTC do último bounce registrado |
| **PotentialBounceCount** | Number | | Não | Quantidade acumulada de bounces potenciais |
| **FirstDateUTC** | Date | | Não | Data e hora UTC do primeiro bounce registrado |
| **EID** | Number | | Não | ID da conta Enterprise no SFMC |
| **ChannelName** | Text | 50 | Não | Nome do canal de mensageria configurado |
| **ChannelID** | Phone | | Não | Número de telefone associado ao canal |
| **ChannelType** | Text | 50 | Não | Tipo de canal (ex: WhatsApp) |
| **EntryDate** | Date | | Não | Data de inserção do registro |
| **ContactKey** | Text | 255 | Não | Chave do Contato (identificador único no SFMC) |

#### Tabela: DEX_WHATSAPP_TRACKING_SEND

- **Descrição:** Armazena os registros específicos dos disparos de WhatsApp efetuados no sistema.
- **Origem:** Exportação offline via Automation Studio (Chat Messaging Extract).

##### Estrutura de Campos (DEX_WHATSAPP_TRACKING_SEND)

| Campo | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| **SendIdentifier** | Text | 50 | **Sim** | Identificador único do envio (Equivalente ao JobID para identificar o disparo) |
| **EventDateUTC** | Date | | Não | Data e hora em UTC do evento de envio |
| **SendType** | Text | 50 | Não | Tipo de envio executado |
| **ActivityName** | Text | 400 | Não | Nome da atividade no Journey Builder |
| **ContactKey** | Text | 255 | Não | Chave do Contato (identificador único no SFMC) |
| **ConversationType** | Text | 50 | Não | Categoria de conversa do WhatsApp |
| **ChannelName** | Text | 50 | Não | Nome do canal de mensageria configurado |
| **EntryDate** | Date | | Não | Data de inserção do registro |
| **AssetId** | Number | | Não | ID do conteúdo de mensagem do Content Builder |
| **ChannelID** | Text | 50 | Não | Identificador do canal |
| **EID** | Number | | Não | ID da conta Enterprise no SFMC |
| **AppID** | Text | 50 | Não | ID do aplicativo do canal |
| **JbDefinitionId** | Text | 50 | Não | ID da definição da jornada |
| **Status** | Text | 50 | Não | Status de envio |
| **Reason** | Text | 50 | Não | Motivo do status de envio, se aplicável |
| **JbActivityId** | Text | 50 | Não | ID de atividade da jornada no Journey Builder |
| **TrackingType** | Text | 50 | Não | Tipo de tracking registrado |
| **ChannelType** | Text | 50 | Não | Tipo de canal (ex: WhatsApp) |
| **MID** | Number | | Não | ID da Business Unit remetente |
| **MessageTypeID** | Number | | Não | ID do tipo de mensagem enviado |
| **MobileNumber** | Text | 50 | Não | Número de celular de destino do envio |

---

### 4.2 Canal: MobilePush

Esta seção agrupa as Data Extensions customizadas utilizadas para armazenar dados de envio e comportamento do canal MobilePush da Cemig SIM.

#### Tabela: DEX_PUSH_PUSHSENDLOG

- **Descrição:** Tabela de log de envio (SendLog) específica para o canal de Push Notifications.
- **Origem:** Gravação em tempo real durante disparos de Push no SFMC.

##### Estrutura de Campos (DEX_PUSH_PUSHSENDLOG)

| Campo | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| **EntryDate** | Date | | **Sim** | Data de entrada do log |
| **PushBatchID** | Text | 50 | Não | ID do lote de envio de Push |
| **PushTriggeredSendRequestID** | Text | 50 | Não | ID da requisição de disparo de Push |
| **SubID** | Text | 50 | Não | ID da subinscrição |
| **PushJobID** | Text | 50 | Não | ID do job de disparo do Push (Equivalente ao JobID para identificar o disparo) |
| **AppId** | Text | 50 | Não | ID do aplicativo móvel cadastrado |
| **LogDate** | Date | | Não | Data da gravação do log |
| **DeviceId** | Text | 255 | Não | ID exclusivo do dispositivo do contato |

#### Tabela: DEX_PUSH_DETAIL

- **Descrição:** Armazena o detalhamento de envios, status e engajamento (como aberturas) das mensagens Push, populada a partir da extração do MobilePush Detail Extract.
- **Origem:** Importação offline do MobilePush Detail Extract via Automation Studio.

##### Estrutura de Campos (DEX_PUSH_DETAIL)

| Campo | Tipo no SFMC | Tamanho / Detalhes | Chave Primária? | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| **PrimaryKey** | Text | 400 | **Sim** | Chave primária única gerada para o registro |
| **EntryDate** | Date | | **Sim** | Data de inserção do registro |
| **MessageOpened** | Text | 50 | Não | Indica se a mensagem foi aberta |
| **MessageID** | Number | | Não | ID da mensagem de Push |
| **ContactKey** | Text | 255 | Não | Chave do Contato (identificador único no SFMC) |
| **InboxMessageDownloaded** | Date | | Não | Data de download da mensagem na inbox do app, se aplicável |
| **DateTimeSend** | Date | | Não | Data e hora em que o envio foi efetuado |
| **Platform** | Text | 50 | Não | Plataforma do dispositivo (iOS, Android) |
| **Status** | Text | 50 | Não | Status do envio do Push |
| **SystemToken** | Text | 4000 | Não | Token de push do dispositivo |
| **TimeInApp** | Number | | Não | Tempo gasto no aplicativo após o clique, se aplicável |
| **PlatformVersion** | Text | 50 | Não | Versão do sistema operacional |
| **Format** | Number | | Não | Formato da mensagem |
| **Template** | Text | 50 | Não | Nome do template de mensagem utilizado |
| **AndroidMediaUrl** | Text | 4000 | Não | URL de mídia para dispositivos Android |
| **MessageContent** | Text | 4000 | Não | Conteúdo de texto da mensagem enviada |
| **RequestId** | Text | 50 | Não | ID da requisição de disparo |
| **PageName** | Text | 4000 | Não | Nome da página de destino/redirecionamento |
| **InboxMessageOpened** | Date | | Não | Data de abertura da mensagem na inbox do app |
| **GeofenceName** | Text | 4000 | Não | Nome da área geográfica (Geofence) associada, se aplicável |
| **AppName** | Text | 50 | Não | Nome do aplicativo móvel |
| **Campaigns** | Text | 100 | Não | Nomes das campanhas associadas |
| **IosMediaUrl** | Text | 4000 | Não | URL de mídia para dispositivos iOS |
| **OpenDate** | Date | | Não | Data e hora de abertura da notificação pelo usuário |
| **MediaAlt** | Text | 4000 | Não | Texto alternativo de mídia |
| **PushJobId** | Text | 50 | Não | ID do job de disparo do Push (Equivalente ao JobID para identificar o disparo) |
| **MessageName** | Text | 255 | Não | Nome comercial da mensagem de Push |
| **DeviceId** | Text | 255 | Não | ID exclusivo do dispositivo do contato |
| **ServiceResponse** | Text | 400 | Não | Resposta do serviço de push (APNS/FCM) |

---

## 5. Diretrizes para Criação e SQL

Ao escrever Queries SQL para carregar dados para as Data Extensions de disparo (DEX):

1. **Remoção de Duplicidades**: Garanta que haja um `ROW_NUMBER() OVER (PARTITION BY Id ORDER BY LastModifiedDate DESC)` se a base `Lead_Salesforce` contiver múltiplos registros para o mesmo lead.
2. **Preenchimento de Campos Obrigatórios**: Campos obrigatórios de controle ou personalização não devem conter valores nulos na query.
3. **Mapeamento Amigável**: Lembre-se de mapear os campos na query conforme a convenção do cliente:
   - `Lead.Name AS NomeCompleto`
   - `Lead.TelefoneFormatadoWhats__c AS Telefone`
   - `Lead.LinkParaAmbienteDeAssinatura__c AS LinkAssinatura`
   - `'br' AS Localidade`

# Module: WhatsApp (Chat Messaging)

> **Skill**: sfmc-copilot | **Categoria**: Mobile Messaging | **Tokens**: ~3K

---

## Visão Geral

O **WhatsApp** no Salesforce Marketing Cloud (Chat Messaging / WhatsApp Message Activities) permite enviar mensagens transacionais e promocionais automatizadas a partir do Journey Builder, usando templates homologados pela Meta e número oficial de WhatsApp Business (WABA).

> **📌 IMPORTANTE — Tracking de WhatsApp:**
> Não existe uma DataView nativa do SFMC específica para WhatsApp (ex: `_WhatsAppMessageTracking` **não existe**).
> O rastreamento granular exige o processo de **Chat Messaging Data Extracts** via Automation Studio.
> Para KPIs agregados (taxa de entrega, leitura etc.), utilize o **Marketing Cloud Intelligence (Datorama)**.

---

## Conceitos Fundamentais

| Conceito | Descrição |
|----------|-----------|
| **WABA** | WhatsApp Business Account — conta oficial da empresa gerenciada via Meta Business Suite |
| **Template de Mensagem** | Modelo pré-aprovado pela Meta para envios ativos (iniciados pela marca) |
| **Chat Messaging** | Nome da feature no SFMC que gerencia envios de WhatsApp via Journey Builder |
| **Janela de 24h** | Após o cliente iniciar uma conversa, a marca pode enviar mensagens livres por 24h |
| **Opt-in** | Consentimento explícito obrigatório antes de qualquer disparo |

---

## Tipos de Template (Meta)

| Tipo | Quando usar |
|------|-------------|
| **Marketing** | Ofertas, promoções, reengajamento de leads |
| **Utility** | Confirmações, acompanhamento de processos, faturas |
| **Authentication** | Códigos OTP de uso único |

---

## Journey Builder com WhatsApp

### Atividade: WhatsApp Message
A atividade de WhatsApp é arrastada dentro do fluxo da jornada igual a um envio de e-mail.

```
Entry: Lead parado no CRM (D+15)
    ↓
Decision Split: Tem número de WhatsApp com opt-in?
    ├── SIM → WhatsApp Message (Template homologado)
    └── NÃO → Fallback Email Marketing
```

### Personalização com Variáveis de Template (AMPscript)

Templates do WhatsApp aceitam variáveis no formato `{{1}}`, `{{2}}` que são preenchidas no painel da atividade no Journey Builder. Use AMPscript para pré-processar os valores:

```ampscript
%%[
SET @PrimeiroNome = AttributeValue("FirstName")
SET @LinkLP       = AttributeValue("CustomLinkLP")

IF Empty(@PrimeiroNome) THEN
  SET @PrimeiroNome = "Torcedor"
ENDIF
]%%

/* No painel do Journey Builder, mapear:
   Variável {{1}} → %%=v(@PrimeiroNome)=%%
   Variável {{2}} → %%=v(@LinkLP)=%%        */
```

---

## Melhores Práticas de URL Dinâmica no WhatsApp (Pós-domínio)

- **Regra de Homologação da Meta:** A Meta exige que a URL base de botões em templates do WhatsApp (ex: `https://meudominio.com/`) seja fixa no momento da aprovação do template. Apenas query strings ou caminhos finais dinâmicos são aceitos como variáveis.
- **Extração de Pós-domínio no SQL:** Ao preparar os dados para o disparo de WhatsApp no SFMC, a query SQL deve extrair apenas os parâmetros a partir do caractere de interrogação `?` (ou do caminho final do link).
- **Snippet SQL (Extração da Query String):**
  ```sql
  SUBSTRING(LinkOrigem, CHARINDEX('?', LinkOrigem), 8000) AS UrlBotao
  ```
- **Configuração no Journey Builder:** Mapeie a variável correspondente à URL dinâmica no painel do WhatsApp no Journey Builder (ex: `{{2}}`), o que fará com que a Meta monte o link completo corretamente: `https://meudominio.com/{{2}}`.

---

## Rastreamento de WhatsApp — Como Obter os Dados

### ⚠️ Não existe DataView nativa para WhatsApp

Ao contrário do e-mail (`_Sent`, `_Open`, `_Click`), **o SFMC não possui uma DataView nativa para mensagens de WhatsApp**. Existem dois caminhos dependendo da necessidade:

---

### Opção 1 — KPIs Agregados: Intelligence Reports (Marketing Cloud Intelligence / Datorama)

**Quando usar:** O usuário precisa de métricas gerais como taxa de entrega, leitura, falha — sem precisar do dado por assinante individual.

- Acesse: **Marketing Cloud Intelligence** (antigo Datorama) → Aba **WhatsApp Engagement**
- O painel nativo exibe: Mensagens Enviadas, Entregues, Lidas, Taxa de Leitura, Falhas e Opt-outs.
- Referência oficial: [WhatsApp Engagement Dashboard](https://help.salesforce.com/s/articleView?id=mktg.mc_dat_dash_whatsapp_engage.htm&type=5)

> 💡 **Diretriz da Skill:** Se o usuário perguntar sobre métricas/KPIs de WhatsApp, **oriente para o Intelligence Reports** como primeira resposta.

---

### Opção 2 — Dados Granulares por Assinante: Chat Messaging Data Extract

**Quando usar:** O usuário precisa de dados individuais por contato (quem recebeu, quem leu, quem falhou) para análise em SQL, relatórios personalizados ou alimentar outras DEs.

O processo é feito via **Automation Studio** usando a atividade de **Data Extract**.

#### Passo a passo para configurar o Chat Messaging Data Extract:

1. **Acesse o Automation Studio** → Crie uma nova Automation.
2. Arraste a atividade **Data Extract**.
3. Em "Extract Type", selecione **Chat Messaging Detail Extract** (`ChatMessagingDetailExtract`).
4. Configure os parâmetros:
   - **Date Range:** intervalo de datas desejado (ex: últimos 7 dias).
   - **File Naming Pattern:** defina o nome do arquivo de saída (ex: `whatsapp_tracking_%%Year%%%%Month%%%%Day%%.csv`).
5. Adicione uma atividade de **File Transfer** após o Data Extract para mover o arquivo gerado do Safe Harbor para um FTP ou importar diretamente para uma Data Extension.
6. **(Opcional)** Adicione uma atividade de **Import File** para importar o CSV gerado diretamente em uma Data Extension do SFMC.
7. **Agende** a automação para rodar diariamente (ex: às 01h00).

- Referência oficial: [Chat Messaging Data Extract](https://help.salesforce.com/s/articleView?id=mktg.mc_jb_whatsapp_data_extract.htm&type=5)

#### Campos típicos gerados pelo ChatMessagingDetailExtract:

| Campo | Descrição |
|-------|-----------|
| `SubscriberKey` | Identificador único do contato |
| `MobileNumber` | Número de telefone (com DDI) |
| `JourneyName` | Nome da jornada de origem |
| `JourneyVersion` | Versão da jornada |
| `ActivityName` | Nome da atividade de WhatsApp dentro da jornada |
| `TemplateName` | Nome do template enviado |
| `Status` | Status do envio: `Sent`, `Delivered`, `Read`, `Failed`, `Undelivered` |
| `SentDate` | Data e hora do envio |
| `DeliveredDate` | Data e hora da entrega |
| `ReadDate` | Data e hora da leitura |
| `ErrorCode` | Código de erro em caso de falha |
| `ErrorDescription` | Descrição do erro |

---

## Fluxo de Decisão para Dados de WhatsApp

```
Usuário quer dados de WhatsApp?
    │
    ├── Quer KPIs gerais (taxa entrega, leitura, falhas)?
    │       └── → Oriente para o Intelligence Reports (Datorama)
    │
    └── Quer dados granulares por assinante (análise individual, SQL)?
            │
            ├── Já tem uma DE com o ChatMessagingDetailExtract configurado?
            │       ├── SIM → Solicite: nome da DE, campos disponíveis e estrutura
            │       │         → Monte queries SQL personalizadas para esse cliente
            │       └── NÃO → Oriente o passo a passo do Data Extract acima
```

---

## Modelo de DE para Importação do Extract (Esquema Sugerido)

Após configurar o extract, crie uma DE no SFMC com esta estrutura para receber os dados importados:

| Nome do Campo | Tipo | Comprimento | É PK? |
|---------------|------|-------------|-------|
| `SubscriberKey` | Text | 254 | ✅ |
| `MobileNumber` | Text | 50 | |
| `JourneyName` | Text | 200 | |
| `ActivityName` | Text | 200 | |
| `TemplateName` | Text | 200 | |
| `Status` | Text | 50 | |
| `SentDate` | Date | — | |
| `DeliveredDate` | Date | — | |
| `ReadDate` | Date | — | |
| `ErrorCode` | Text | 50 | |
| `ErrorDescription` | Text | 500 | |

---

## SQL de Exemplo (após importação do Extract para DE personalizada)

```sql
-- Taxa de leitura por template de WhatsApp
SELECT
    TemplateName,
    COUNT(*)                                               AS TotalEnviado,
    SUM(CASE WHEN Status = 'Delivered' THEN 1 ELSE 0 END) AS Entregues,
    SUM(CASE WHEN Status = 'Read'      THEN 1 ELSE 0 END) AS Lidos,
    SUM(CASE WHEN Status = 'Failed'    THEN 1 ELSE 0 END) AS Falhas,
    CAST(
      SUM(CASE WHEN Status = 'Read' THEN 1.0 ELSE 0 END)
      / NULLIF(SUM(CASE WHEN Status = 'Delivered' THEN 1.0 ELSE 0 END), 0)
      * 100 AS DECIMAL(5,2)
    ) AS TaxaDeLeitura_Pct
FROM [WhatsApp_Tracking_DE]   -- substitua pelo nome real da DE do cliente
WHERE SentDate >= DATEADD(DAY, -30, GETDATE())
GROUP BY TemplateName
ORDER BY TotalEnviado DESC
```

---

## Compliance e Opt-in / Opt-out

- **Consentimento Explícito:** Colhido obrigatoriamente no site, app ou formulário antes do primeiro envio.
- **Opt-out:** Palavras como *SAIR*, *STOP*, *PARAR* precisam ser tratadas com fluxo automático de desinscrição que atualiza a flag de opt-in na DE do assinante.
- **Regras da Meta:** Templates de marketing são revisados pela Meta. Rejeições frequentes podem levar ao bloqueio do número.

---

## Referências Oficiais

- [Chat Messaging Data Extract (Tracking Granular)](https://help.salesforce.com/s/articleView?id=mktg.mc_jb_whatsapp_data_extract.htm&type=5)
- [WhatsApp Engagement Dashboard — Intelligence Reports](https://help.salesforce.com/s/articleView?id=mktg.mc_dat_dash_whatsapp_engage.htm&type=5)
- [Meta Business — WhatsApp Business Guidelines](https://developers.facebook.com/docs/whatsapp/guidelines)

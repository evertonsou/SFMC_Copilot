# Module: MobileConnect (SMS)

> **Skill**: sfmc-copilot | **Categoria**: Mobile Messaging | **Tokens**: ~2K

---

## Visão Geral

**MobileConnect** é o módulo de SMS/MMS do Salesforce Marketing Cloud. Permite enviar mensagens de texto, gerenciar keywords, configurar short codes e criar jornadas mobile.

---

## Conceitos Fundamentais

| Conceito | Descrição |
|----------|-----------|
| **Short Code** | Número de 5-6 dígitos para envios em massa (ex: 77765) |
| **Long Code** | Número de 10 dígitos para envios 1:1 |
| **Toll-Free Number** | Número 0800 para SMS nos EUA |
| **Keyword** | Palavra-chave que o subscriber envia para interagir |
| **MO (Mobile Originated)** | Mensagem do subscriber para a empresa |
| **MT (Mobile Terminated)** | Mensagem da empresa para o subscriber |
| **Double Opt-in** | Confirmação de inscrição via SMS |

---

## Keywords

### Tipos de Keyword

| Keyword | Função |
|---------|--------|
| `STOP` | Opt-out obrigatório (CAN-SPAM/CTIA) |
| `HELP` | Retorna informações de contato |
| `JOIN` / `YES` | Opt-in para receber mensagens |
| Customizada | Ação específica definida pelo usuário |

### Configurar Keyword

1. **MobileConnect** → **Keywords** → **Create**
2. Definir palavra-chave (ex: `NEWSLETTER`)
3. Configurar resposta automática (MT response)
4. Associar ao Short Code
5. Definir ação: Subscribe, Unsubscribe, Opt-in, Custom

### Exemplo de Fluxo com Keyword

```
Subscriber envia: "JOIN" para 77765

SFMC processa:
  1. Registra subscriber no MobileConnect
  2. Envia confirmação: "Você foi inscrito! 
     Para cancelar, envie STOP a qualquer momento."

Subscriber responde "STOP":
  1. SFMC processa opt-out automaticamente
  2. Remove do canal SMS (mantém outros canais)
  3. Envia: "Você foi removido. Não receberá mais SMS."
```

---

## Short Code vs. Long Code

| Aspecto | Short Code | Long Code |
|---------|-----------|-----------|
| Velocidade | 100+ msg/seg | 1 msg/seg |
| Volume | Alto (milhares/seg) | Baixo (1:1) |
| Custo | Alto | Baixo |
| Aprovação | 4-8 semanas | Imediato |
| Uso | Campanhas em massa | Alertas individuais |

---

## Mensagens SMS

### Limites de Caracteres

| Codificação | Limite por SMS | Concatenado (2 SMS) |
|-------------|---------------|---------------------|
| **GSM-7** (ASCII padrão) | 160 chars | 306 chars |
| **Unicode** (caracteres especiais) | 70 chars | 134 chars |

> ⚠️ Caracteres como `ã`, `ç`, `é` ativam Unicode automaticamente — reduz limite para 70.

### Criar Mensagem SMS

1. MobileConnect → **Mobile Messages** → **Create**
2. Tipo: **Outbound Message** ou **Triggered Message**
3. Selecionar Short Code
4. Redigir mensagem (contador de caracteres visível)
5. Configurar personalizações (tokens)

### Personalização em SMS (AMPscript)

```ampscript
%%[
/* SMS aceita AMPscript básico */
SET @nome = AttributeValue("FirstName")
SET @valor = AttributeValue("OrderValue")
SET @pedido = AttributeValue("OrderID")
]%%

Olá %%=v(@nome)=%%! Seu pedido #%%=v(@pedido)=%% de R$%%=v(@valor)=%% foi confirmado. Dúvidas? Ligue 0800-123-456. STOP p/ cancelar.
```

---

## Opt-in e Opt-out (Compliance)

### Requisitos Obrigatórios (CTIA / LGPD)

```
✅ TODA mensagem deve incluir opt-out: "STOP para cancelar"
✅ Primeiro opt-out deve ser processado imediatamente
✅ Confirmar opt-out: "Você foi removido. Não receberá mais SMS."
✅ Consentimento explícito antes do primeiro envio
✅ Indicar quem está enviando (nome da empresa) no SMS
✅ Frequência esperada deve ser comunicada no opt-in
```

### Fluxo de Double Opt-in SMS

```
Etapa 1: Subscriber envia "JOIN" para 77765
  ↓
Etapa 2: SFMC envia confirmação:
  "Para confirmar inscrição de SMS da [Empresa], 
   responda SIM. Msg recorrentes. Tarifa padrão.
   STOP para cancelar."
  ↓
Etapa 3: Subscriber responde "SIM"
  ↓
Etapa 4: SFMC confirma:
  "Inscrição confirmada! Você receberá 
   até 4 SMS/mês da [Empresa]."
```

---

## Triggered SMS (Transacional)

### Enviar SMS via REST API

```javascript
// POST /sms/v1/messageContact/{messageID}/send
var payload = {
  "mobileNumbers": ["+5511987654321"],
  "Subscribe": true,
  "Resubscribe": false,
  "keyword": "JOIN",
  "Override": false,
  "MessageText": "Seu código: 123456. Válido por 5 min."
};
```

### Enviar SMS via SSJS

```javascript
<script runat="server">
Platform.Load("Core", "1");

function sendTriggeredSMS(messageKey, mobileNumber, attributes) {
  var tSend = TriggeredSend.Init(messageKey);
  
  var sub = {
    EmailAddress: mobileNumber + "@mobile.com",
    SubscriberKey: mobileNumber,
    MobileNumber: mobileNumber,
    Attributes: attributes || []
  };
  
  var result = tSend.Send(sub);
  return result;
}

// Uso
var result = sendTriggeredSMS(
  "SMS_OTP_KEY",
  "+5511987654321",
  [{ Name: "OTPCode", Value: "789456" }]
);
</script>
```

---

## Journey Builder com SMS

### Atividades Disponíveis no Journey Builder

| Atividade | Descrição |
|-----------|-----------|
| **Send SMS** | Enviar mensagem SMS |
| **Mobile Push** | Notificação push (requer MobilePush) |
| **Decision Split** | Baseado em resposta do subscriber |
| **Wait** | Aguardar resposta ou tempo |

### Journey: Notificação de Pedido (SMS + Email)

```
Entry: API Event (pedido criado)
    ↓
Wait: 2 minutos
    ↓
Send SMS: "Pedido #{{OrderID}} confirmado! 
           Entrega em 5 dias úteis."
    ↓
Wait: 24 horas
    ↓
Decision Split: Clicou no link do SMS?
    ├── SIM → Send Email: "Acompanhe seu pedido"
    └── NÃO → Send SMS: "Seu pedido está a caminho! 
                          [link de rastreamento]"
```

---

## SQL — DataViews de SMS

### _SMSMessageTracking

```sql
-- Mensagens SMS enviadas nos últimos 30 dias
SELECT
  smt.MobileNumber,
  smt.MessageID,
  smt.MessageText,
  smt.Status,        -- Sent, Delivered, Failed
  smt.EventDate,
  smt.ShortCode
FROM _SMSMessageTracking smt
WHERE smt.EventDate >= DATEADD(DAY, -30, GETDATE())
ORDER BY smt.EventDate DESC
```

### Taxa de Entrega de SMS

```sql
SELECT
  Status,
  COUNT(*) AS Total,
  CAST(COUNT(*) AS FLOAT) / SUM(COUNT(*)) OVER() * 100 AS Percentual
FROM _SMSMessageTracking
WHERE EventDate >= DATEADD(DAY, -30, GETDATE())
GROUP BY Status
```

---

## Boas Práticas

```
✅ Sempre inclua "STOP para cancelar" em TODA mensagem
✅ Envie SMS no horário comercial local do destinatário (8h-20h)
✅ Use GSM-7 (sem acentos) para maximizar caracteres (160 vs 70)
✅ Personalize com nome — aumenta engajamento em 20-30%
✅ SMS < 160 chars: conciso e direto
✅ Inclua sempre o nome da empresa (Ex: "[NomeMarca]:")
✅ Teste opt-out antes do primeiro envio real
✅ Monitore taxa de opt-out — acima de 3% indica problema
✅ Use double opt-in para garantir consentimento válido
✅ Sincronize opt-outs SMS com o CRM imediatamente
```

---

## Referências

- [MobileConnect Help](https://help.salesforce.com/s/articleView?id=sf.mc_mc_mobileconnect.htm)
- [Keywords Setup](https://help.salesforce.com/s/articleView?id=sf.mc_mc_keywords.htm)
- [_SMSMessageTracking DataView](https://help.salesforce.com/s/articleView?id=sf.mc_as_data_view_sms.htm)
- [CTIA Best Practices](https://www.ctia.org/the-wireless-industry/industry-commitments/messaging-principles-and-best-practices)

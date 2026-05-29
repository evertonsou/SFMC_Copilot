# Module: Journey Advanced

> **Skill**: sfmc-copilot | **Categoria**: Journey Builder | **Tokens**: ~2.5K

---

## Visão Geral

Funcionalidades avançadas do **Journey Builder** além do uso básico: A/B splits sofisticados, Custom Activities, Salesforce Entry Events, Goal tracking e padrões de jornadas complexas.

---

## A/B Split Activities

### Tipos de Split no Journey Builder

| Atividade | Descrição |
|-----------|-----------|
| **Random Split** | Divide aleatoriamente em percentuais |
| **Engagement Split** | Divide por comportamento (abriu/clicou) |
| **Decision Split** | Divide por atributos do contato/DE |
| **Einstein Split** | Divide por score/recomendação Einstein |

---

## Random Split — A/B Test Avançado

### Configurar Teste Multivariado (A/B/C)

```
Entry Source
    ↓
Random Split (33% / 33% / 34%)
  ├── A (33%): Email versão A → Wait → Engagement Split
  ├── B (33%): Email versão B → Wait → Engagement Split
  └── C (34%): Email versão C → Wait → Engagement Split
```

### Identificar Variante no AMPscript

```ampscript
%%[
/* A variante é passada via atributo da jornada */
SET @variante = AttributeValue("JourneyVariant")

IF @variante == "A" THEN
  SET @assunto  = "Novidades imperdíveis chegaram!"
  SET @headline = "Descubra o que há de novo"
  SET @cta      = "Explorar Agora"
ELSEIF @variante == "B" THEN
  SET @assunto  = "Só hoje: 20% de desconto"
  SET @headline = "Oferta exclusiva por tempo limitado"
  SET @cta      = "Aproveitar Desconto"
ELSE
  SET @assunto  = "Uma surpresa especial para você"
  SET @headline = "Preparamos algo especial"
  SET @cta      = "Ver Surpresa"
ENDIF
]%%
```

---

## Engagement Split (Por Comportamento)

### Configurar

1. Journey Builder → Adicionar **Engagement Split** após Email activity
2. Selecionar e-mail referência
3. Definir tempo de avaliação (ex: 3 dias após envio)
4. Configurar branches:

```
Engagement Split (após email, espera 3 dias):
  ├── Abriu o email → Path: "Engajados"
  ├── Clicou em link específico → Path: "Mais Interessados"
  └── Não abriu → Path: "Inativos"
```

### Encadeamento de Splits

```
Email 1
  ↓
Engagement Split (2 dias)
  ├── Clicou → Wait 1 dia → Email de Aprofundamento
  ├── Abriu (sem clicar) → Wait 2 dias → Email com CTA mais direto
  └── Não abriu → Wait 3 dias → Email com assunto diferente
                    ↓
              Engagement Split 2 (2 dias)
                ├── Abriu → Continuar jornada
                └── Não abriu → Sunset / Exit
```

---

## Salesforce Entry Events

### Tipos de Entry Event (CRM)

| Evento | Trigger |
|--------|---------|
| **Salesforce Data Event** | Campo de objeto Salesforce muda |
| **Campaign Member Entry** | Adicionado a Campaign Salesforce |
| **Joureny Event** | Evento customizado via API |

### Configurar Salesforce Data Event

1. Journey Builder → **New Journey**
2. Entry Source: **Salesforce Data**
3. Selecionar objeto: `Contact`, `Lead`, `Opportunity` ou Custom Object
4. Definir critério: ex. `StageName = "Closed Won"` AND `Amount > 10000`
5. Configurar campos para trazer para a jornada
6. Definir evaluation criteria: `New and Updated Records`

### Campos Injetados no Journey (AMPscript)

```ampscript
%%[
/* Campos do objeto Salesforce disponíveis como atributos */
SET @accountName = AttributeValue("Account.Name")
SET @oppValue    = AttributeValue("Amount")
SET @ownerEmail  = AttributeValue("Owner.Email")
SET @closeDate   = AttributeValue("CloseDate")
SET @sfContactID = AttributeValue("ContactId")
]%%

Olá! Seu contrato com a %%=v(@accountName)=%% foi aprovado.
Valor: R$ %%=FormatNumber(@oppValue, "N2", "pt-BR")=%%
Data de fechamento: %%=Format(@closeDate, "dd/MM/yyyy")=%%
```

---

## Custom Activities

### O que são Custom Activities
Atividades personalizadas que integram **sistemas externos** diretamente no Journey Builder via REST API. Permitem executar ações em sistemas de terceiros como CRMs, ERPs, plataformas de suporte.

### Arquitetura de Custom Activity

```
Journey Builder
    ↓ (webhook)
Custom Activity Config UI (iframe hosted externally)
    ↓ (execution request)
External API Endpoint
    ↓ (response)
Journey Builder continua
```

### Etapas de Implementação

```
1. Criar app no SFMC (Installed Package)
2. Criar endpoint REST que recebe: 
   - POST /execute  → executa a ação
   - POST /publish  → ativa a atividade
   - POST /validate → valida configuração
   - GET /config    → retorna config da UI
3. Hospedar UI de configuração (iframe)
4. Registrar no Journey Builder
```

### Payload de Execução (Recebido pelo Endpoint)

```json
{
  "inArguments": [
    { "contactKey": "12345" },
    { "emailAddress": "cliente@empresa.com" },
    { "customField": "valor-configurado"  }
  ],
  "outArguments": [],
  "activityObjectID": "abc-123",
  "journeyId": "journey-uuid",
  "activityId": "activity-uuid",
  "requestId": "request-uuid",
  "keyValue": "subscriber-key"
}
```

### Resposta do Endpoint de Custom Activity

```json
// Sucesso
{ "branchResult": "true" }

// Falha (journeyer sai)
{ "branchResult": "false" }

// Para atividades com múltiplos branches
{ "branchResult": "branch_name" }
```

---

## Goal Tracking (Metas de Jornada)

### Configurar Goal

1. Journey Builder → **Goal** (aba lateral)
2. Definir critério: ex. DE `DE_Conversions` contém `SubscriberKey`
3. Definir janela de avaliação (ex: 7 dias após entrada)
4. Ativar

### O que acontece quando subscriber atinge o Goal

```
Subscriber entra na jornada
    ↓
(a qualquer momento)
Goal avaliado: subscriber na DE_Conversions?
    ├── SIM → Exit imediato (meta atingida) 
    └── NÃO → Continua a jornada normalmente
```

### SQL para Atualizar DE de Goal

```sql
/* Automation diária para marcar conversões */
INSERT INTO DE_Conversions (SubscriberKey, ConvertedDate)
SELECT DISTINCT
  o.EmailAddress AS SubscriberKey,
  o.EventDate    AS ConvertedDate
FROM DE_Pedidos o
WHERE o.PedidoDate >= DATEADD(DAY, -1, GETDATE())
  AND o.SubscriberKey NOT IN (
    SELECT SubscriberKey FROM DE_Conversions
  )
```

---

## Padrões de Jornadas Avançadas

### Pattern 1: Nurturing com Reentrada

```
Entry: DE_Leads (novo lead)
  ↓
Email 1: "Bem-vindo"
  ↓
Wait: 3 dias
  ↓
Engagement Split:
  ├── Clicou → Email 2: "Conteúdo Aprofundado"
  └── Não clicou → Email 2b: "Resumo Rápido"
  ↓
Wait: 5 dias
  ↓
Decision Split: Lead.Status = "Qualified"?
  ├── SIM → Notify: Sales team via Custom Activity
  └── NÃO → Email 3: "Conteúdo Educativo"
```

### Pattern 2: Re-engagement (Win-Back)

```
Entry: DE_Inativos (180+ dias sem abrir)
  ↓
Email: "Sentimos sua falta" + oferta especial
  ↓
Engagement Split (7 dias):
  ├── Abriu/Clicou → Update DE_Reengaged → Exit (sucesso)
  └── Não abriu → Email 2: "Última chance"
                    ↓
              Engagement Split (7 dias):
                ├── Abriu → Exit (reengajado)
                └── Não abriu → Sunset Email: 
                    "Vamos manter seu cadastro?"
                      ↓
                  Engagement Split (3 dias):
                    ├── Clicou "SIM" → Exit (mantido ativo)
                    └── Sem ação → Unsubscribe Global → Exit
```

### Pattern 3: Onboarding Multi-canal

```
Entry: API Event (cadastro no app)
  ↓
Parallel (simultâneo):
  ├── Canal Email: "Bem-vindo! Aqui está seu guia"
  └── Canal SMS: "Olá {{Nome}}! App ativado ✅"
  ↓
Wait: 1 dia
  ↓
Decision Split: Completou perfil no app?
  ├── SIM → Email: "Próximos passos"
  └── NÃO → Push Notification: "Complete seu perfil"
              ↓
          Wait: 2 dias
              ↓
          Email: "Dicas para começar"
```

---

## Exit Criteria e Re-entry

### Exit Criteria

```
Configurar em: Journey → Settings → Exit Criteria

Opções:
- Subscriber unscreve de lista específica
- Subscriber atinge campo = valor
- Subscriber está em DE de exclusão
```

### Re-entry

```
Journey Settings → Re-entry:
  - No re-entry: subscriber entra apenas uma vez
  - Re-entry allowed: pode entrar novamente
  - Re-entry anytime: entra imediatamente se critério atende
  
⚠️ Cuidado: Re-entry sem controle pode criar loops infinitos
✅ Use Always Re-entry com Exit Criteria para controlar
```

---

## Boas Práticas

```
✅ Desenhe o Journey em papel/Miro antes de construir no SFMC
✅ Sempre configure Exit Criteria para evitar jornadas infinitas
✅ Use Goal Tracking para medir conversão real
✅ Teste com subscriber real em ambiente de sandbox antes de ativar
✅ Nomeie atividades claramente (não "Email 1", mas "Email_Boas_Vindas")
✅ Documente cada Decision Split com a lógica por trás
✅ Configure notificações de erro para Custom Activities
✅ Revise Analytics semanalmente — ajuste waits e sequências
✅ Use version control no Journey Builder para rollback
✅ Limite jornadas a 15-20 atividades para melhor performance
```

---

## Referências

- [Journey Builder Advanced](https://help.salesforce.com/s/articleView?id=sf.mc_jb_journey_builder.htm)
- [Custom Activities Guide](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/creating-activities.html)
- [Salesforce Entry Events](https://help.salesforce.com/s/articleView?id=sf.mc_jb_salesforce_entry_event.htm)
- [Goal Tracking](https://help.salesforce.com/s/articleView?id=sf.mc_jb_goal.htm)

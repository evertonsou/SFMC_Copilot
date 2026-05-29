# Module: Test & Control Sends

> **Skill**: sfmc-copilot | **Categoria**: Testing & Optimization | **Tokens**: ~2K

---

## Visão Geral

**Test & Control Sends** englobam todas as estratégias de validação antes do envio em produção e de testes estatísticos para otimizar campanhas: test sends, A/B tests e grupos de controle (holdout).

---

## Test Send (Envio de Teste)

### Tipos de Test Send no SFMC

| Tipo | Descrição | Quando Usar |
|------|-----------|-------------|
| **Preview** | Visualização estática sem envio | Verificar layout |
| **Test Send** | Envia para endereços específicos | Validar renderização real |
| **Subscriber Preview** | Preview como subscriber específico | Verificar personalização |
| **Proof Send** | Versão aprovação para stakeholders | Review pré-envio |

### Como Fazer Test Send

1. Email Studio → Selecionar email → **Preview & Test**
2. Clicar em **Test Send**
3. Adicionar endereços de destino (máx. 5 por vez)
4. Selecionar DE para dados de personalização (opcional)
5. Confirmar envio

### Test Send via AMPscript — Simular Contexto

```ampscript
%%[
/* Forçar contexto de teste (remover em produção) */
SET @isTest = "false"

/* Em teste, usar dados fixos */
IF @isTest == "true" THEN
  SET @nome  = "Teste Usuário"
  SET @email = "teste@empresa.com"
  SET @tier  = "Gold"
ELSE
  SET @nome  = AttributeValue("FirstName")
  SET @email = AttributeValue("EmailAddress")
  SET @tier  = Lookup("DE_Tiers", "Tier", "Email", @email)
ENDIF
]%%

Olá, %%=v(@nome)=%%!
```

### Boas Práticas de Test Send

```
✅ Teste em Gmail, Outlook (Windows/Mac/Web), Apple Mail e mobile
✅ Verifique todos os links de unsubscribe e preferências
✅ Confirme personalização com dados reais de subscriber de teste
✅ Verifique o assunto e preheader em dispositivos móveis
✅ Teste modo escuro
✅ Valide rastreamento de cliques (UTM params)
✅ Confirme que imagens carregam (sem bloqueio de domínio)
```

---

## A/B Testing

### Tipos de A/B Test no SFMC

| Tipo | O que Testar |
|------|-------------|
| **Subject Line** | Assunto do e-mail |
| **From Name** | Nome do remetente |
| **Content** | Corpo do e-mail (versões diferentes) |
| **Send Time** | Horário de envio |
| **Preheader** | Texto de preheader |

### Configurar A/B Test no Email Studio

1. Email Studio → **A/B Testing** → **New A/B Test**
2. Selecionar tipo de teste
3. Definir:
   - **Tamanho do grupo de teste**: % da lista (ex: 20% para A, 20% para B)
   - **Duração do teste**: 4, 8, 12, 24 horas
   - **Critério de vitória**: Open Rate, Click Rate, Manual
4. Configurar envio do **Winner** (restante da lista)
5. Ativar

### Configurar A/B Test no Journey Builder

1. Journey Builder → Adicionar atividade **Random Split**
2. Definir proporções (50%/50% ou 70%/30%)
3. Cada branch recebe versão diferente do email
4. Monitorar métricas no Journey Analytics

### Calculadora de Significância Estatística

```
Para resultados confiáveis:
- Tamanho mínimo por grupo: 1.000 subscribers
- Duração mínima: 4 horas (para aberturas imediatas)
- Significância estatística: ≥ 95%
- Diferença mínima detectável: ≥ 2% de open rate

Fórmula simplificada de p-value: usar ferramentas como
- abtestguide.com/calc
- optimizely.com/sample-size-calculator
```

### SQL — Analisar Resultados de A/B Test

```sql
/* Comparar performance entre variantes A e B */
SELECT
  j.EmailName,
  j.JobID,
  COUNT(DISTINCT s.SubscriberKey)             AS TotalSent,
  COUNT(DISTINCT o.SubscriberKey)             AS TotalOpens,
  COUNT(DISTINCT c.SubscriberKey)             AS TotalClicks,
  CAST(COUNT(DISTINCT o.SubscriberKey) AS FLOAT) /
    NULLIF(COUNT(DISTINCT s.SubscriberKey), 0) * 100 AS OpenRate,
  CAST(COUNT(DISTINCT c.SubscriberKey) AS FLOAT) /
    NULLIF(COUNT(DISTINCT o.SubscriberKey), 0) * 100 AS ClickToOpenRate
FROM _Job j
JOIN _Sent s ON j.JobID = s.JobID
LEFT JOIN _Open o ON j.JobID = o.JobID AND s.SubscriberKey = o.SubscriberKey
LEFT JOIN _Click c ON j.JobID = c.JobID AND s.SubscriberKey = c.SubscriberKey
WHERE j.EmailName IN ('Newsletter_A_Test', 'Newsletter_B_Test')
  AND j.SchedTime >= DATEADD(DAY, -7, GETDATE())
GROUP BY j.EmailName, j.JobID
ORDER BY j.EmailName
```

---

## Holdout Groups (Grupo de Controle)

### O que é um Holdout Group
Um grupo de controle é um **subconjunto da audiência que NÃO recebe** o email. Serve para medir o **incremento real** da campanha (lift).

```
Audiência Total: 100.000

Grupo A (Tratamento): 90.000 → Recebe o email
Grupo B (Controle):   10.000 → NÃO recebe o email

Lift = (Conversão do Grupo A) - (Conversão do Grupo B)
```

### Implementar Holdout no Journey Builder

1. Journey Builder → Random Split após o Entry
2. Configurar: **90% Tratamento / 10% Controle**
3. Branch Controle → **Wait** (duração da jornada) → Exit
4. Branch Tratamento → Email activities normais
5. Monitorar ambos os grupos via Journey Analytics

### Criar Holdout via SQL (Automation)

```sql
/* Selecionar 10% aleatório para controle */
SELECT TOP 10 PERCENT
  SubscriberKey,
  EmailAddress,
  'Control' AS GroupType,
  GETDATE() AS AssignedDate
FROM DE_TargetAudience
ORDER BY NEWID()  -- Aleatoriedade

/* Os 90% restantes vão para Tratamento */
```

### DE de Holdout

```
Nome: DE_HoldoutGroups
Campos:
  SubscriberKey  Text(36)  PK
  EmailAddress   Email
  CampaignName   Text(100)
  GroupType      Text(20)   (Control / Treatment)
  AssignedDate   Date
  ConvertedDate  Date       (preencher pós-conversão)
  Converted      Boolean
```

### SQL — Calcular Lift da Campanha

```sql
/* Comparar conversão entre tratamento e controle */
SELECT
  h.GroupType,
  COUNT(DISTINCT h.SubscriberKey)                  AS TotalSubscribers,
  SUM(CASE WHEN h.Converted = 1 THEN 1 ELSE 0 END) AS TotalConverted,
  CAST(SUM(CASE WHEN h.Converted = 1 THEN 1 ELSE 0 END) AS FLOAT) /
    NULLIF(COUNT(DISTINCT h.SubscriberKey), 0) * 100 AS ConversionRate
FROM DE_HoldoutGroups h
WHERE h.CampaignName = 'Campanha_BlackFriday_2026'
GROUP BY h.GroupType
```

---

## Aprovação Multi-estágio (Proof Workflow)

### Configurar Workflow de Aprovação

1. Email Studio → **Admin** → **Approval Workflow**
2. Criar workflow com etapas:
   - Designer (criação)
   - Revisor 1 (conteúdo)
   - Revisor 2 (compliance/legal)
   - Aprovador final
3. Configurar notificações por e-mail
4. Ativar

### Status de Aprovação

| Status | Descrição |
|--------|-----------|
| `Draft` | Em desenvolvimento |
| `Pending Approval` | Aguardando revisores |
| `Approved` | Pronto para envio |
| `Rejected` | Voltou para revisão |
| `Sent` | Enviado |

---

## Checklist Completo Pré-Envio

```
CONTEÚDO:
✅ Subject line testado (< 50 caracteres)
✅ Preheader configurado (40-90 caracteres)
✅ From Name e From Email corretos
✅ Personalização verificada com dados reais
✅ Links testados (todos funcionam)
✅ Unsubscribe link presente e funcional
✅ Endereço físico no rodapé (CAN-SPAM)

RENDERIZAÇÃO:
✅ Gmail (Web + App)
✅ Outlook (Windows + Mac + Web)
✅ Apple Mail (Mac + iOS)
✅ Android Gmail/Mail
✅ Modo escuro testado
✅ Imagens com alt text

TÉCNICO:
✅ SPF/DKIM verificados
✅ Score de spam < 3 (mail-tester.com)
✅ Links UTM configurados
✅ Pixel de tracking ativo
✅ DE de audiência validada
✅ Suppression list aplicada
✅ Horário de envio definido (STO ou manual)
```

---

## Referências

- [A/B Testing SFMC](https://help.salesforce.com/s/articleView?id=sf.mc_es_ab_testing.htm)
- [Journey Builder Random Split](https://help.salesforce.com/s/articleView?id=sf.mc_jb_random_split.htm)
- [AB Test Guide Calculator](https://abtestguide.com/calc/)

# Module: Engagement Scoring

> **Skill**: sfmc-copilot | **Categoria**: Analytics & Segmentation | **Tokens**: ~2.5K

---

## Visão Geral

**Engagement Scoring** é um modelo numérico que classifica subscribers pela sua **probabilidade de interação**. Permite priorizar contatos de alto valor, reativar inativos e proteger a reputação do domínio evitando envios para base fria.

---

## Modelo de Scoring Básico (RFM)

### RFM — Recência, Frequência, Monetário

| Dimensão | Descrição | Peso Sugerido |
|----------|-----------|--------------|
| **R (Recência)** | Quando abriu/clicou pela última vez | 40% |
| **F (Frequência)** | Quantas vezes engajou nos últimos 90 dias | 40% |
| **M (Monetário)** | Valor gerado (se disponível) | 20% |

---

## Modelo de Scoring por Ações de E-mail

### Tabela de Pontos

| Ação | Pontos | Janela |
|------|--------|--------|
| Abriu e-mail | +10 | últimos 30 dias |
| Clicou em link | +20 | últimos 30 dias |
| Abriu e-mail | +5 | 31-60 dias |
| Clicou em link | +10 | 31-60 dias |
| Abriu e-mail | +2 | 61-90 dias |
| Não abriu (30d) | -5 | últimos 30 dias |
| Não abriu (60d) | -10 | últimos 60 dias |
| Bounce suave | -15 | qualquer |
| Spam complaint | -100 | qualquer |

---

## SQL — Calcular Engagement Score

### Score Simples por Recência e Frequência

```sql
/* Calcular engagement score para os últimos 90 dias */
SELECT
  s.SubscriberKey,
  s.EmailAddress,

  /* Recência: dias desde última abertura */
  DATEDIFF(DAY, MAX(o.EventDate), GETDATE()) AS DaysSinceLastOpen,

  /* Frequência: total de aberturas */
  COUNT(DISTINCT o.JobID) AS TotalOpens,

  /* Cliques */
  COUNT(DISTINCT c.JobID) AS TotalClicks,

  /* Score calculado */
  CASE
    WHEN DATEDIFF(DAY, MAX(o.EventDate), GETDATE()) <= 30
      THEN (COUNT(DISTINCT o.JobID) * 10) + (COUNT(DISTINCT c.JobID) * 20)
    WHEN DATEDIFF(DAY, MAX(o.EventDate), GETDATE()) <= 60
      THEN (COUNT(DISTINCT o.JobID) * 5) + (COUNT(DISTINCT c.JobID) * 10)
    WHEN DATEDIFF(DAY, MAX(o.EventDate), GETDATE()) <= 90
      THEN (COUNT(DISTINCT o.JobID) * 2) + (COUNT(DISTINCT c.JobID) * 5)
    ELSE 0
  END AS EngagementScore

FROM _Subscribers s
LEFT JOIN _Open o
  ON s.SubscriberKey = o.SubscriberKey
  AND o.EventDate >= DATEADD(DAY, -90, GETDATE())
LEFT JOIN _Click c
  ON s.SubscriberKey = c.SubscriberKey
  AND c.EventDate >= DATEADD(DAY, -90, GETDATE())
WHERE s.Status = 'Active'
GROUP BY s.SubscriberKey, s.EmailAddress
```

### Score Avançado com Penalizações

```sql
SELECT
  s.SubscriberKey,
  s.EmailAddress,
  
  /* Base positiva */
  ISNULL(opens30.TotalOpens * 10, 0)  +
  ISNULL(clicks30.TotalClicks * 20, 0) +
  ISNULL(opens60.TotalOpens * 5, 0)   +
  ISNULL(clicks60.TotalClicks * 10, 0) +
  ISNULL(opens90.TotalOpens * 2, 0)   +
  ISNULL(clicks90.TotalClicks * 5, 0)  -
  
  /* Penalizações */
  ISNULL(bounces.TotalBounces * 15, 0) -
  ISNULL(complaints.Total * 100, 0)
  
  AS EngagementScore

FROM _Subscribers s

LEFT JOIN (
  SELECT SubscriberKey, COUNT(DISTINCT JobID) AS TotalOpens
  FROM _Open WHERE EventDate >= DATEADD(DAY,-30,GETDATE())
  GROUP BY SubscriberKey
) opens30 ON s.SubscriberKey = opens30.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(DISTINCT JobID) AS TotalClicks
  FROM _Click WHERE EventDate >= DATEADD(DAY,-30,GETDATE())
  GROUP BY SubscriberKey
) clicks30 ON s.SubscriberKey = clicks30.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(DISTINCT JobID) AS TotalOpens
  FROM _Open WHERE EventDate BETWEEN DATEADD(DAY,-60,GETDATE()) AND DATEADD(DAY,-31,GETDATE())
  GROUP BY SubscriberKey
) opens60 ON s.SubscriberKey = opens60.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(DISTINCT JobID) AS TotalClicks
  FROM _Click WHERE EventDate BETWEEN DATEADD(DAY,-60,GETDATE()) AND DATEADD(DAY,-31,GETDATE())
  GROUP BY SubscriberKey
) clicks60 ON s.SubscriberKey = clicks60.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(DISTINCT JobID) AS TotalOpens
  FROM _Open WHERE EventDate BETWEEN DATEADD(DAY,-90,GETDATE()) AND DATEADD(DAY,-61,GETDATE())
  GROUP BY SubscriberKey
) opens90 ON s.SubscriberKey = opens90.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(DISTINCT JobID) AS TotalClicks
  FROM _Click WHERE EventDate BETWEEN DATEADD(DAY,-90,GETDATE()) AND DATEADD(DAY,-61,GETDATE())
  GROUP BY SubscriberKey
) clicks90 ON s.SubscriberKey = clicks90.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(*) AS TotalBounces
  FROM _Bounce WHERE BounceType = 'SoftBounce'
  AND EventDate >= DATEADD(DAY,-90,GETDATE())
  GROUP BY SubscriberKey
) bounces ON s.SubscriberKey = bounces.SubscriberKey

LEFT JOIN (
  SELECT SubscriberKey, COUNT(*) AS Total
  FROM _Complaint WHERE EventDate >= DATEADD(DAY,-90,GETDATE())
  GROUP BY SubscriberKey
) complaints ON s.SubscriberKey = complaints.SubscriberKey

WHERE s.Status = 'Active'
```

---

## Tier Segmentation (Classificação por Tier)

### Definição de Tiers

| Tier | Score | Descrição | Estratégia |
|------|-------|-----------|-----------|
| **Platinum** | ≥ 150 | Champions — engajam sempre | Exclusividade, early access |
| **Gold** | 100–149 | Engajados regularmente | Conteúdo de valor, loyalty |
| **Silver** | 50–99 | Engajam ocasionalmente | Frequência moderada, relevância |
| **Bronze** | 10–49 | Baixo engajamento | Reativação, menos e-mails |
| **At-Risk** | 1–9 | Quase inativos | Campanha win-back |
| **Inactive** | 0 | Sem engajamento | Sunset policy |

### SQL — Classificar por Tier

```sql
/* Aplicar tier ao score calculado */
SELECT
  SubscriberKey,
  EmailAddress,
  EngagementScore,
  CASE
    WHEN EngagementScore >= 150 THEN 'Platinum'
    WHEN EngagementScore >= 100 THEN 'Gold'
    WHEN EngagementScore >= 50  THEN 'Silver'
    WHEN EngagementScore >= 10  THEN 'Bronze'
    WHEN EngagementScore >= 1   THEN 'At-Risk'
    ELSE 'Inactive'
  END AS EngagementTier
FROM DE_EngagementScores
ORDER BY EngagementScore DESC
```

---

## Automation — Atualizar Scores Automaticamente

### Configuração no Automation Studio

```
Nome: AUTO_Update_Engagement_Scores
Schedule: Toda segunda-feira às 06:00
Atividades:
  1. SQL Query Activity → "Calcular EngagementScore" → DE_EngagementScores (Overwrite)
  2. SQL Query Activity → "Aplicar Tiers" → DE_EngagementTiers (Overwrite)
  3. SQL Query Activity → "Identificar At-Risk" → DE_AtRisk (Overwrite)
  4. Email Activity → Notificação interna (opcional)
```

---

## Usar Scores em Jornadas

### Journey: Campanha por Tier

```
Entry: DE_EngagementTiers (toda semana)

Decision Split: Qual tier?
  ├── Platinum → Email Exclusivo VIP + Sem limite de frequência
  ├── Gold     → Email Padrão Premium
  ├── Silver   → Email Padrão (máx 2x/semana)
  ├── Bronze   → Email Básico (máx 1x/semana)
  ├── At-Risk  → Email Win-Back
  └── Inactive → Sunset Email ou Exit
```

### AMPscript — Personalizar por Tier

```ampscript
%%[
SET @tier = Lookup("DE_EngagementTiers", "EngagementTier", "SubscriberKey", _subscriberkey)
SET @score = Lookup("DE_EngagementScores", "EngagementScore", "SubscriberKey", _subscriberkey)

IF @tier == "Platinum" THEN
  SET @badge    = "🏆 Cliente Platinum"
  SET @desconto = "20%"
  SET @mensagem = "Como nosso cliente mais especial, você tem acesso exclusivo."
ELSEIF @tier == "Gold" THEN
  SET @badge    = "⭐ Cliente Gold"
  SET @desconto = "15%"
  SET @mensagem = "Aproveite benefícios exclusivos do programa Gold."
ELSE
  SET @badge    = "Cliente Especial"
  SET @desconto = "10%"
  SET @mensagem = "Temos uma oferta especial para você."
ENDIF
]%%

<div class="badge">%%=v(@badge)=%%</div>
<p>%%=v(@mensagem)=%%</p>
<strong>Seu desconto: %%=v(@desconto)=%%</strong>
```

---

## Sunset Policy (Inativos)

### Fluxo Recomendado para Inativos

```
180 dias sem abertura:
  └── Enviar "Win-Back Email 1": "Sentimos sua falta"
  
210 dias sem abertura:
  └── Enviar "Win-Back Email 2": "Última chance — Oferta especial"
  
240 dias sem abertura:
  └── Enviar "Sunset Email": "Vamos manter seu cadastro ativo?"
      ├── Clicou → Manter ativo, resetar score
      └── Não clicou → Mover para DE_Sunset, parar envios

365 dias:
  └── Considerar remoção definitiva (LGPD compliance)
```

### SQL — Identificar para Sunset

```sql
SELECT
  s.SubscriberKey,
  s.EmailAddress,
  DATEDIFF(DAY, MAX(ISNULL(o.EventDate, s.DateJoined)), GETDATE()) AS DaysInactive
FROM _Subscribers s
LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey
WHERE s.Status = 'Active'
GROUP BY s.SubscriberKey, s.EmailAddress, s.DateJoined
HAVING DATEDIFF(DAY, MAX(ISNULL(o.EventDate, s.DateJoined)), GETDATE()) >= 180
ORDER BY DaysInactive DESC
```

---

## Boas Práticas

```
✅ Recalcule scores pelo menos semanalmente
✅ Não envie para Inactive — prejudica deliverability
✅ Segmente campanhas por tier para mensagens mais relevantes
✅ Defina e documente sua tabela de pontuação antes de implementar
✅ Combine engagement score com dados CRM para enriquecer o modelo
✅ Monitore distribuição dos tiers mensalmente — mudanças indicam problemas
✅ Use Einstein Predictive Scoring como complemento ao modelo customizado
✅ Implemente sunset policy para proteger reputação do IP/domínio
```

---

## Referências

- [_Open DataView](https://help.salesforce.com/s/articleView?id=sf.mc_as_data_view_open.htm)
- [_Click DataView](https://help.salesforce.com/s/articleView?id=sf.mc_as_data_view_click.htm)
- [Einstein Predictive Scoring](https://help.salesforce.com/s/articleView?id=sf.mc_einstein_predictive_scoring.htm)
- [Sunset Policy Guide](https://help.salesforce.com/s/articleView?id=sf.mc_es_sunset_policy.htm)

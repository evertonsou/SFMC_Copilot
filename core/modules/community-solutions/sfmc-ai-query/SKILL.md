# Module: SFMC AI Query Assistant

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1K  
> **Repositório**: [sfmc-ai-query-assistant](https://github.com/sfmcdeveloper/sfmc-ai-query-assistant) ⭐ 11

---

## Visão Geral

**sfmc-ai-query-assistant** é uma ferramenta que usa **GPT-4 / IA generativa** para converter descrições em linguagem natural em queries SQL para Salesforce Marketing Cloud. Ideal para usuários menos técnicos que precisam criar relatórios e segmentações.

---

## Casos de Uso

```
✅ "Me mostre todos os subscribers que abriram no último mês mas não clicaram"
✅ "Quantos e-mails foram enviados por campanha nos últimos 90 dias?"
✅ "Liste os top 100 engajados dos últimos 6 meses"
✅ "Encontre subscribers com mais de 3 bounces nos últimos 30 dias"
✅ "Quais são as taxas de abertura por domínio de e-mail?"
```

---

## Como Funciona

```
Usuário digita: "Mostre subscribers que abriram mas não clicaram em 30 dias"
        ↓
AI processa: analisa DataViews disponíveis + estrutura SQL SFMC
        ↓
Output: Query SQL pronta para uso no Automation Studio
```

---

## Contexto que a IA Precisa Conhecer (Prompt Engineering)

Para obter melhores resultados ao usar IA (GPT, Claude, Gemini) para gerar queries SFMC, inclua este contexto:

```
CONTEXTO PARA IA:
Você é um especialista em Salesforce Marketing Cloud SQL.
O SFMC usa SQL Server dialect (T-SQL).
As principais tabelas/DataViews são:
- _Sent (JobID, SubscriberKey, EmailAddress, EventDate)
- _Open (JobID, SubscriberKey, EventDate, IsUnique)
- _Click (JobID, SubscriberKey, URL, EventDate, IsUnique)
- _Bounce (JobID, SubscriberKey, BounceType, EventDate)
- _Unsubscribe (JobID, SubscriberKey, EventDate)
- _Subscribers (SubscriberKey, EmailAddress, Status, DateJoined)
- _Job (JobID, EmailName, SchedTime, DeliveredTime)

Regras:
- Use DATEADD(DAY, -N, GETDATE()) para datas relativas
- Use DISTINCT para evitar duplicatas
- Use LEFT JOIN para incluir todos os enviados
- O SFMC não suporta CTEs (WITH...) em Query Activities
- Use subqueries ao invés de CTEs
```

---

## Exemplos de Queries Geradas por IA

### "Subscribers que abriram mas não clicaram nos últimos 30 dias"

```sql
SELECT DISTINCT
  s.SubscriberKey,
  s.EmailAddress,
  MAX(o.EventDate) AS LastOpen
FROM _Sent se
JOIN _Open o ON se.JobID = o.JobID AND se.SubscriberKey = o.SubscriberKey
LEFT JOIN _Click c ON se.JobID = c.JobID AND se.SubscriberKey = c.SubscriberKey
JOIN _Subscribers s ON se.SubscriberKey = s.SubscriberKey
WHERE se.EventDate >= DATEADD(DAY, -30, GETDATE())
  AND s.Status = 'Active'
  AND c.SubscriberKey IS NULL  -- Não clicou
GROUP BY s.SubscriberKey, s.EmailAddress
ORDER BY LastOpen DESC
```

### "Taxa de abertura por domínio nos últimos 90 dias"

```sql
SELECT
  LOWER(SUBSTRING(s.EmailAddress, CHARINDEX('@', s.EmailAddress) + 1, LEN(s.EmailAddress))) AS Domain,
  COUNT(DISTINCT se.SubscriberKey)                                    AS TotalSent,
  COUNT(DISTINCT o.SubscriberKey)                                     AS TotalOpened,
  CAST(COUNT(DISTINCT o.SubscriberKey) AS FLOAT) /
    NULLIF(COUNT(DISTINCT se.SubscriberKey), 0) * 100                AS OpenRate
FROM _Sent se
JOIN _Subscribers s ON se.SubscriberKey = s.SubscriberKey
LEFT JOIN _Open o ON se.JobID = o.JobID AND se.SubscriberKey = o.SubscriberKey
WHERE se.EventDate >= DATEADD(DAY, -90, GETDATE())
GROUP BY LOWER(SUBSTRING(s.EmailAddress, CHARINDEX('@', s.EmailAddress) + 1, LEN(s.EmailAddress)))
HAVING COUNT(DISTINCT se.SubscriberKey) >= 100  -- Apenas domínios com volume relevante
ORDER BY OpenRate DESC
```

### "Performance por e-mail nos últimos 7 dias"

```sql
SELECT
  j.EmailName,
  j.JobID,
  j.SchedTime,
  COUNT(DISTINCT se.SubscriberKey)                                   AS Sent,
  COUNT(DISTINCT o.SubscriberKey)                                    AS Opened,
  COUNT(DISTINCT c.SubscriberKey)                                    AS Clicked,
  COUNT(DISTINCT b.SubscriberKey)                                    AS Bounced,
  COUNT(DISTINCT u.SubscriberKey)                                    AS Unsubscribed,
  CAST(COUNT(DISTINCT o.SubscriberKey) AS FLOAT) /
    NULLIF(COUNT(DISTINCT se.SubscriberKey), 0) * 100               AS OpenRate,
  CAST(COUNT(DISTINCT c.SubscriberKey) AS FLOAT) /
    NULLIF(COUNT(DISTINCT o.SubscriberKey), 0) * 100               AS ClickToOpenRate
FROM _Job j
JOIN _Sent se ON j.JobID = se.JobID
LEFT JOIN _Open o ON j.JobID = o.JobID AND se.SubscriberKey = o.SubscriberKey AND o.IsUnique = 1
LEFT JOIN _Click c ON j.JobID = c.JobID AND se.SubscriberKey = c.SubscriberKey AND c.IsUnique = 1
LEFT JOIN _Bounce b ON j.JobID = b.JobID AND se.SubscriberKey = b.SubscriberKey
LEFT JOIN _Unsubscribe u ON j.JobID = u.JobID AND se.SubscriberKey = u.SubscriberKey
WHERE j.SchedTime >= DATEADD(DAY, -7, GETDATE())
GROUP BY j.EmailName, j.JobID, j.SchedTime
ORDER BY j.SchedTime DESC
```

---

## Prompt Templates para SFMC SQL

### Template Base

```
Crie uma query SQL para Salesforce Marketing Cloud que:
[DESCRIÇÃO DO QUE VOCÊ QUER]

Contexto técnico:
- Use T-SQL (SQL Server syntax)
- DataViews disponíveis: _Sent, _Open, _Click, _Bounce, _Unsubscribe, _Subscribers, _Job
- NÃO use CTEs (WITH...)
- Use DATEADD(DAY, -N, GETDATE()) para períodos relativos
- Adicione comentários explicando cada parte
```

### Template para Segmentação

```
Crie uma query SQL para SFMC que selecione subscribers para segmentação:
CRITÉRIOS: [descrever quem quer incluir e quem quer excluir]
OUTPUT: SubscriberKey, EmailAddress e os campos relevantes
PERÍODO: [último N dias/meses]
DESTINO: Será usada como Query Activity no Automation Studio
```

---

## Validação de Queries Geradas por IA

```
Antes de usar qualquer query gerada por IA no SFMC:

✅ 1. Verificar sintaxe T-SQL (sem CTEs, sem funções não-suportadas)
✅ 2. Checar se colunas existem nas DataViews (esquema pode variar)
✅ 3. Adicionar DISTINCT onde necessário (evitar duplicatas)
✅ 4. Testar com SELECT TOP 10 antes do SELECT completo
✅ 5. Verificar performance (EXPLAIN não disponível — monitorar tempo)
✅ 6. Validar resultados contra expectativa manual
```

---

## Links

- **GitHub**: https://github.com/sfmcdeveloper/sfmc-ai-query-assistant
- **Complemento**: Usar com o módulo `sql/SKILL.md` desta skill
- **DataViews Reference**: Módulo `dataviews/SKILL.md`

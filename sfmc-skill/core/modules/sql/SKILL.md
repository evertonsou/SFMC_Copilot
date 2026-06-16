---
name: sfmc-sql
description: SQL Query Activities and Query Studio for Salesforce Marketing Cloud. Patterns, syntax, deduplication with ROW_NUMBER, and pagination.
---

# SFMC SQL Module

SQL para Query Activities e Query Studio no Marketing Cloud.

## Características do SQL

| Feature | Support |
|---------|---------|
| SELECT | ✅ |
| JOIN | ✅ |
| WHERE | ✅ |
| GROUP BY | ✅ |
| ORDER BY | ✅ |
| LIMIT/TOP | ✅ (LIMIT) |
| Subqueries | ✅ |
| UNION | ✅ |
| ROW_NUMBER() | ✅ |

## Limitações

- Sem DELETE direto (use Data Extension actions)
- Sem UPDATE direto (use Data Extract)
- Funções limitadas vs SQL padrão
- Max execution time: 30 minutos

## Deduplicação com ROW_NUMBER()

**ROW_NUMBER() é a melhor opção para deduplicação** porque permite controlar exatamente quais registros manter.

### deduplicação por Email (Manter mais recente)

```sql
SELECT 
    SubscriberKey,
    EmailAddress,
    Status,
    DateJoined
FROM (
    SELECT 
        SubscriberKey,
        EmailAddress,
        Status,
        DateJoined,
        ROW_NUMBER() OVER (PARTITION BY EmailAddress ORDER BY DateJoined DESC) AS RowNum
    FROM _Subscribers
) AS Dedup
WHERE RowNum = 1
```

### Deduplicação por múltiplos campos

```sql
SELECT 
    SubscriberKey,
    EmailAddress,
    CampaignName,
    EventDate
FROM (
    SELECT 
        SubscriberKey,
        EmailAddress,
        CampaignName,
        EventDate,
        ROW_NUMBER() OVER (PARTITION BY SubscriberKey, CampaignName ORDER BY EventDate DESC) AS RowNum
    FROM _Sent
    WHERE CampaignName IS NOT NULL
) AS Dedup
WHERE RowNum = 1
```

### Manter registro mais antigo

```sql
-- Manter o primeiro registro (ORDER BY ASC)
SELECT *
FROM (
    SELECT 
        SubscriberKey,
        EmailAddress,
        DateJoined,
        ROW_NUMBER() OVER (PARTITION BY EmailAddress ORDER BY DateJoined ASC) AS RowNum
    FROM _Subscribers
) AS Dedup
WHERE RowNum = 1
```

### Deduplicação com GROUP BY (alternativa simples)

```sql
-- Quando só precisa de agregação (sem escolher qual registro manter)
SELECT 
    EmailAddress,
    MIN(DateJoined) AS FirstJoinDate,
    MAX(DateJoined) AS LastJoinDate,
    COUNT(*) AS TotalRecords
FROM _Subscribers
GROUP BY EmailAddress
HAVING COUNT(*) > 1
```

## Paginação (Selecionar intervalo de linhas)

Para selecionar registros entre linhas específicas (ex: linhas 100-150).

### Padrão OFFSET/FETCH (Recomendado)

```sql
-- Selecionar linhas 100 a 150 (OFFSET 99, LIMIT 51)
SELECT 
    SubscriberKey,
    EmailAddress,
    Status,
    DateJoined
FROM _Subscribers
WHERE Status = 'Active'
ORDER BY DateJoined DESC
OFFSET 99 ROWS
FETCH NEXT 51 ROWS ONLY
```

### Alternativa com ROW_NUMBER()

```sql
-- Paginação manual
SELECT 
    SubscriberKey,
    EmailAddress,
    Status,
    DateJoined
FROM (
    SELECT 
        SubscriberKey,
        EmailAddress,
        Status,
        DateJoined,
        ROW_NUMBER() OVER (ORDER BY DateJoined DESC) AS RowNum
    FROM _Subscribers
) AS Paginated
WHERE RowNum BETWEEN 100 AND 150
```

### Exemplos práticos de paginação

```sql
-- Primeiros 100 registros
SELECT TOP 100 * FROM _Subscribers ORDER BY DateJoined DESC

-- Próximos 100 (após os primeiros 100)
SELECT * FROM (
    SELECT 
        SubscriberKey,
        EmailAddress,
        ROW_NUMBER() OVER (ORDER BY DateJoined DESC) AS RowNum
    FROM _Subscribers
) AS paginated
WHERE RowNum BETWEEN 101 AND 200
```

## Query Activities

```sql
-- Query básica
SELECT 
    SubscriberKey,
    EmailAddress,
    Status,
    DateJoined
FROM _Subscribers
WHERE Status = 'Active'

-- Com JOIN
SELECT 
    s.SubscriberKey,
    s.EmailAddress,
    o.EventDate AS LastOpenDate,
    c.EventDate AS LastClickDate
FROM _Subscribers s
LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey
LEFT JOIN _Click c ON s.SubscriberKey = c.SubscriberKey
WHERE s.Status = 'Active'
```

## Patterns Comuns

### De-duplicate + Filtrar
```sql
SELECT 
    SubscriberKey,
    EmailAddress,
    DateJoined
FROM (
    SELECT 
        SubscriberKey,
        EmailAddress,
        DateJoined,
        ROW_NUMBER() OVER (PARTITION BY EmailAddress ORDER BY DateJoined DESC) AS RowNum
    FROM _Subscribers
    WHERE Status = 'Active'
) AS dedup
WHERE RowNum = 1
AND DateJoined >= DATEADD(day, -30, GETDATE())
```

### Engagement Segmentation com ROW_NUMBER
```sql
SELECT 
    SubscriberKey,
    EmailAddress,
    CASE 
        WHEN LastOpen >= DATEADD(day, -30, GETDATE()) THEN 'Active'
        WHEN LastOpen >= DATEADD(day, -90, GETDATE()) THEN 'Engaged'
        ELSE 'Lapsed'
    END AS EngagementTier
FROM (
    SELECT 
        s.SubscriberKey,
        s.EmailAddress,
        MAX(o.EventDate) AS LastOpen,
        ROW_NUMBER() OVER (PARTITION BY s.SubscriberKey ORDER BY MAX(o.EventDate) DESC) AS RowNum
    FROM _Subscribers s
    LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey
    WHERE s.Status = 'Active'
    GROUP BY s.SubscriberKey, s.EmailAddress
) AS engagement
```

### Recent Sends Tracking
```sql
SELECT 
    s.SubscriberKey,
    s.EmailAddress,
    s.EventDate AS SendDate,
    o.EventDate AS OpenDate,
    c.EventDate AS ClickDate,
    b.EventDate AS BounceDate
FROM _Sent s
LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey AND s.JobID = o.JobID
LEFT JOIN _Click c ON s.SubscriberKey = c.SubscriberKey AND s.JobID = c.JobID
LEFT JOIN _Bounce b ON s.SubscriberKey = b.SubscriberKey AND s.JobID = b.JobID
WHERE s.JobID = 12345
```

## Funções Disponíveis

### String
- LEN, SUBSTRING, CHARINDEX, REPLACE, LTRIM, RTRIM, UPPER, LOWER

### Date
- GETDATE, DATEADD, DATEDIFF, YEAR, MONTH, DAY, DATEPART

### Math
- ABS, CEILING, FLOOR, ROUND, SIGN

### Null Handling
- ISNULL, NULLIF

## Data Extension Target

```sql
SELECT 
    EmailAddress AS Email,
    GETDATE() AS ExtractDate
FROM _Subscribers
WHERE Status = 'Active'
```

## Order By e Limit

```sql
-- TOP equivalent (SFMC usa LIMIT)
SELECT TOP 1000
    SubscriberKey,
    EmailAddress
FROM _Subscribers
WHERE Status = 'Active'
ORDER BY DateJoined DESC

-- Ou
SELECT 
    SubscriberKey,
    EmailAddress
FROM _Subscribers
WHERE Status = 'Active'
ORDER BY DateJoined DESC
LIMIT 1000
```

## ROW_NUMBER() vs GROUP BY

| Cenário | Melhor Opção |
|---------|--------------|
|需要一个唯一 registro com dados específicos | ROW_NUMBER() |
| Apenas agregar (count, sum, min, max) | GROUP BY |
| Remover duplicatas mantendo um campo específico | ROW_NUMBER() |
| Contar duplicatas | GROUP BY com HAVING COUNT(*) > 1 |
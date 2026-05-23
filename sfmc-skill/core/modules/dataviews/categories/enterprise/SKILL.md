---
name: sfmc-dataviews-enterprise
description: Enterprise-specific System DataViews for multi-tenant Marketing Cloud implementations.
---

# DataViews - Enterprise

## ENT._Subscribers
Subscriber da enterprise (cross-BU).

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| EmailAddress | string | Email |
| Status | string | Status |
| DateJoined | datetime | Data de criação |
| Locale | string | Localidade |
| GlobalID | int | ID Global |

## ENT._EnterpriseAttribute
Atributos customizados enterprise.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| AttributeID | int | ID do atributo |
| AttributeName | string | Nome do atributo |
| AttributeValue | string | Valor |

## ENT._ListSubscribers
Todos os subscribers por lista enterprise-wide.

## ENT._DataFolder
Pastas do sistema.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| ID | int | ID da pasta |
| Name | string | Nome |
| ParentFolder.ID | int | ID da pasta pai |
| Type | string | Tipo (DataExtension, Automations, etc.) |

## Consulta Cross-BU

```sql
SELECT 
    ENT.SubscriberKey,
    ENT.EmailAddress,
    ENT.Status,
    -- Atributos customizados
    (SELECT AttributeValue 
     FROM ENT._EnterpriseAttribute 
     WHERE AttributeName = 'Region' 
     AND SubscriberKey = ENT.SubscriberKey) AS Region
FROM ENT._Subscribers ENT
WHERE ENT.Status = 'Active'
```

## Padrão ENT.

Para acessar DataViews enterprise-wide, prefixe com `ENT.`:
- `ENT._Subscribers`
- `ENT._EnterpriseAttribute`
- `ENT._DataFolder`
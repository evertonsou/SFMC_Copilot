---
name: sfmc-data-extension
description: Data Extension design, field types, keys, relationships, and best practices.
---

# SFMC Data Extension Module

Design e estruturação de Data Extensions.

## Field Types

| Type | Description | Example |
|------|-------------|---------|
| EmailAddress | Email validation | test@example.com |
| Phone | Phone validation | +55 11 99999-9999 |
| Text | String (max 4000) | "Text value" |
| Number | Integer or Decimal | 123, 45.67 |
| Decimal | Decimal with precision | 123.456 |
| Date | Date/DateTime | 2024-01-15 |
| Boolean | True/False | 1/0, true/false |
| Locale | Locale code | pt-BR |
| Boolean | Yes/No | yes/no |

## Keys

### Primary Key
- Identificador único da row
- Obrigatório para algumas operações
- Unique constraint
- Ex: Email, SubscriberKey, OrderID

### Secondary Key
- Index para performance
- Não impõe unique
- Melhora queries

### Composite Key
- Múltiplas colunas como PK
- Combinação única

```sql
-- Exemplo: Composite Key
PRIMARY KEY (Email, OrderID, ProductID)
```

## Relationships

| Type | Description |
|------|-------------|
| One-to-One | 1 row matches 1 row |
| One-to-Many | 1 row matches many |
| Many-to-Many | Via junction table |

### Example: Customer → Orders
```
Customers DE
├── CustomerID (PK)
├── Email
└── Name

Orders DE
├── OrderID (PK)
├── CustomerID (FK)
├── OrderDate
└── Total
```

## Design Patterns

### Sendable DE
```
Campo: EmailAddress ou SubscriberKey
Index: EmailAddress (como envios usam este campo)
Status: Relacionado a _Subscribers
```

### Transactional DE
```
Fields:
- SendableKey (PK)
- EventType
- Payload
- SentDate
- Status
```

### Logging DE
```
Fields:
- LogID (PK)
- Timestamp
- EventType
- Data
- Processed (boolean)
```

## Best Practices

1. **Naming**: `DE_Category_Object` (ex: `DE_Sales_Orders`)
2. **CustomerKey**: Mesmo do nome, sem espaços
3. **Indexes**: Adicione em campos usados em WHERE
4. **Sendable**: Marque campo sendable correto
5. **Data Types**: Use o mais específico possível
6. **Defaults**: Evite NULL com defaults
7. **Retention**: Configure se necessário

## Create via API

```javascript
<script runat="server">
var api = new Script.Util.WSProxy();

var de = {
  Name: "NewDE",
  CustomerKey: "new-de-key",
  Fields: [
    { Name: "Email", Type: "EmailAddress", IsPrimaryKey: true, IsRequired: true },
    { Name: "Name", Type: "String", MaxLength: 100 },
    { Name: "CreatedDate", Type: "Date", IsRequired: false },
    { Name: "IsActive", Type: "Boolean", DefaultValue: true }
  ]
};

var result = api.createItem("DataExtension", de);
</script>
```

## Retention Settings

- Keep all records
- Delete after X days
- Rolling retention period
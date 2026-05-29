---
name: sfmc-ampscript-data-extension
description: AmpScript functions for Data Extension operations in Salesforce Marketing Cloud.
---

# AmpScript - Data Extension Functions

## Funções de Data Extension

### Lookup
Retorna linhas de uma Data Extension baseadas em critérios.

```ampscript
LookupRows(DataExtensionName, ColumnName, Value)
LookupRows(DataExtensionName, ColumnName1, Value1, ColumnName2, Value2)
```

**Exemplo:**
```ampscript
%%[
  SET @rows = LookupRows("Customers", "Country", "Brazil")
  SET @rowCount = RowCount(@rows)
  
  IF @rowCount > 0 THEN
    SET @row = Row(@rows, 1)
    SET @name = Field(@row, "FirstName")
  ENDIF
]%%
```

### Lookup
Retorna um valor único.

```ampscript
Lookup(DataExtensionName, ColumnToReturn, ColumnName, Value)
```

### InsertData
Insere uma nova linha.

```ampscript
InsertData(DataExtensionName, Column1, Value1, Column2, Value2, ...)
```

### UpdateData
Atualiza linhas existentes.

```ampscript
UpdateData(DataExtensionName, NumMatches, ColumnName1, Value1, ColumnName2, Value2, ...)
```

### UpsertData
Insere ou atualiza (determinado por chave).

```ampscript
UpsertData(DataExtensionName, NumberOfKeyColumns, KeyColumn1, KeyValue1, ...)
```

### DeleteData
Remove linhas.

```ampscript
DeleteData(DataExtensionName, ColumnName, Value)
```

### DEExtract
Extrai valores únicos para uso em loops.

```ampscript
DEExtract(DataExtensionName, ColumnToReturnFrom, ColumnName, Value)
```

## Patterns Comuns

### De-duplicate
```ampscript
%%[
  SET @emails = DEExtract("Unsubscribes", "EmailAddress", "Status", "Unsubscribed")
  SET @emailCount = RowCount(@emails)
]%%
```

### Get Subscriber Info
```ampscript
%%[
  SET @subscriberKey = AttributeValue("_subscriberKey")
  SET @rows = LookupRows("ProfileAttributes", "SubscriberKey", @subscriberKey)
]%%
```
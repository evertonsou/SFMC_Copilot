---
name: sfmc-ampscript
description: AmpScript functions reference for Salesforce Marketing Cloud. Covers Data Extension operations, string manipulation, date/time, formatting, HTTP, encryption, and personalization.
---

# SFMC AmpScript Module

AmpScript é uma linguagem de script server-side usada para personalização e lógica em emails e landing pages.

## Visão Geral

| Categoria | Funções Principais |
|-----------|-------------------|
| Data Extension | Lookup, LookupRows, InsertData, UpdateData, DeleteData, UpsertData |
| String | Concat, Replace, Substring, IndexOf, Length, Lower, Upper |
| Date/Time | Now, DateAdd, DateDiff, Format, Parse |
| Formatting | FormatCurrency, FormatNumber, FormatDate |
| HTTP | HTTPGet, HTTPPost, HTTPGetHeader |
| Encryption | Encrypt, Decrypt, EncryptSymmetric |
| Logic | IIF, IF/THEN/ELSE, FOR |
| Personalization | TreatAs, TreatAsContent, TreatAsInlineContent |

## Carregamento de Sub-módulos

Carregue categorias específicas:

- `ampscript/data-extension` - Operações em Data Extensions
- `ampscript/string` - Manipulação de strings
- `ampscript/datetime` - Funções de data/hora
- `ampscript/formatting` - Formatação de dados
- `ampscript/http` - Requisições HTTP
- `ampscript/encryption` - Criptografia
- `ampscript/personalization` - Personalização avançada
- `ampscript/logic` - Estruturas condicionais e loops

## Referência Rápida

### Lookup
```ampscript
%%[
  SET @rows = LookupRows("DataExtensionName", "ColumnName", "Value")
  SET @rowCount = RowCount(@rows)
]%%
```

### InsertData
```ampscript
%%[
  InsertData("DE_Name", "Email", @email, "Name", @name)
]%%
```

### UpdateData
```ampscript
%%[
  UpdateData("DE_Name", 1, "Email", @email, "Status", "Active")
]%%
```

### HTTPGet
```ampscript
%%[
  SET @response = HTTPGet("https://api.example.com/data")
]%%
```

### FormatDate
```ampscript
%%[
  SET @today = Now()
  SET @formatted = FormatDate(@today, "dd/MM/yyyy", "pt-BR")
]%%
```
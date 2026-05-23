---
name: sfmc-ampscript-formatting
description: AmpScript formatting functions for numbers, currency, and text.
---

# AmpScript - Formatting Functions

## Funções de Formatação

### FormatCurrency
Formata como moeda.

```ampscript
FormatCurrency(Value)
FormatCurrency(Value, FormatLocale, CurrencySymbol)
```

### FormatNumber
Formata números.

```ampscript
FormatNumber(Value, Format)
FormatNumber(1234.567, "N2")  → "1,234.57"
FormatNumber(0.25, "P0")     → "25%"
```

### FormatDate
Formata datas (ver também datetime module).

```ampscript
FormatDate(Date, OutputFormat, InputFormat, Locale)
```

### Format
Formata genérico.

```ampscript
Format("Name: %s, Age: %d", @name, @age)
```

### PadLeft / PadRight
Preenche com caracteres.

```ampscript
PadLeft(String, Length, PadChar)
PadRight(String, Length, PadChar)
```

## Códigos de Formato

| Código | Significado | Exemplo |
|--------|-------------|---------|
| N0 | Número sem decimais | 1,234 |
| N1 | 1 casa decimal | 1,234.6 |
| N2 | 2 casas decimais | 1,234.56 |
| P0 | Percentual sem decimais | 25% |
| P1 | Percentual 1 casa | 25.5% |
| D | Número inteiro | 01234 |
| C | Moeda | R$ 1,234.56 |

## Exemplos

```ampscript
%%[
  SET @price = 1234.56
  SET @formattedPrice = FormatCurrency(@price, "pt-BR", "R$")
  
  SET @percentage = 0.255
  SET @formattedPercent = FormatNumber(@percentage, "P0")
  
  SET @code = PadLeft(@id, 6, "0")
]%%
```
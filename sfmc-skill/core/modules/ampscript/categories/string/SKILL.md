---
name: sfmc-ampscript-string
description: AmpScript string manipulation functions.
---

# AmpScript - String Functions

## Funções de String

### Concat
Concatena strings.

```ampscript
Concat(String1, String2, ...)
Concat(String1, "|", String2)
```

### StringFormat
Formata string com placeholders.

```ampscript
StringFormat("Hello %s, your order is %d", @name, @orderId)
```

### Lower / Upper
Converte para minúsculas/maiúsculas.

```ampscript
Lower(String)
Upper(String)
```

### Replace
Substitui texto.

```ampscript
Replace(String, SearchValue, ReplaceValue)
```

### Substring
Extrai parte de uma string.

```ampscript
Substring(String, StartIndex, Length)
```

### IndexOf
Retorna posição de um caractere.

```ampscript
IndexOf(String, SearchValue)
```

### Length
Retorna tamanho da string.

```ampscript
Length(String)
```

### Trim / TrimLeft / TrimRight
Remove espaços.

```ampscript
Trim(String)
TrimLeft(String)
TrimRight(String)
```

### IIF
Condicional inline.

```ampscript
IIF(Condition, TrueValue, FalseValue)
```

## Exemplos

```ampscript
%%[
  SET @fullName = Concat(@firstName, " ", @lastName)
  SET @emailDomain = Substring(@email, IndexOf(@email, "@"), Length(@email))
  SET @upperName = Upper(@fullName)
  SET @cleanEmail = Trim(@email)
]%%
```

```ampscript
%%[
  SET @greeting = IIF(@firstName != "", Concat("Hello ", @firstName), "Hello there")
]%%
```
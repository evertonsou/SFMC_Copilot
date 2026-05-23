---
name: sfmc-ampscript-logic
description: AmpScript logic, conditionals, and loops.
---

# AmpScript - Logic e Control Flow

## Estruturas de Controle

### IF/THEN/ELSE
```ampscript
%%[
  IF condition THEN
    /* code */
  ELSEIF condition2 THEN
    /* code */
  ELSE
    /* code */
  ENDIF
]%%
```

### IIF (Inline If)
```ampscript
IIF(Condition, TrueValue, FalseValue)
```

### FOR Loop
```ampscript
%%[
  FOR @i = 1 TO 10 DO
    /* code */
  NEXT @i
]%%
```

### WHILE Loop
```ampscript
%%[
  SET @continue = "true"
  WHILE @continue == "true" DO
    /* code */
    IF @counter >= 10 THEN
      SET @continue = "false"
    ENDIF
  ENDIF
]%%
```

## Operadores

| Operador | Descrição |
|----------|-----------|
| `==` | Igual |
| `!=` | Diferente |
| `>` | Maior que |
| `<` | Menor que |
| `>=` | Maior ou igual |
| `<=` | Menor ou igual |
| `AND` | E lógico |
| `OR` | OU lógico |
| `NOT` | Negação |

## EMPTY e NULL

```ampscript
EMPTY(Field)      // Retorna true se vazio
EMPTY(@var)
NULL(@var)        // Retorna true se null
```

## Exemplos

```ampscript
%%[
  IF @firstName != "" AND @firstName != NULL THEN
    SET @greeting = Concat("Hello ", @firstName)
  ELSE
    SET @greeting = "Hello Customer"
  ENDIF
]%%
```

```ampscript
%%[
  SET @status = IIF(@points > 1000, "VIP", "Regular")
]%%
```

```ampscript
%%[
  FOR @i = 1 TO @rowCount DO
    SET @row = Row(@rows, @i)
    SET @name = Field(@row, "Name")
    /* process */
  NEXT @i
]%%
```
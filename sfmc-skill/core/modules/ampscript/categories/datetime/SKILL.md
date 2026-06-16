---
name: sfmc-ampscript-datetime
description: AmpScript date and time functions.
---

# AmpScript - Date/Time Functions

## Funções de Data/Hora

### Now
Retorna data/hora atual (GMT).

```ampscript
Now()
```

### LocalDateToSystemDate
Converte hora local para GMT.

```ampscript
LocalDateToSystemDate(Date)
```

### SystemDateToLocalDate
Converte GMT para hora local.

```ampscript
SystemDateToLocalDate(Date)
```

### DateAdd
Adiciona período a uma data.

```ampscript
DateAdd(Date, Number, DatePart)
```

**DatePart**: "Y", "M", "D", "h", "m", "s"

### DateDiff
Calcula diferença entre datas.

```ampscript
DateDiff(Date1, Date2, DatePart)
```

### FormatDate
Formata data para string.

```ampscript
FormatDate(Date, OutputFormat, InputFormat, Locale)
```

### ParseDate
Converte string para data.

```ampscript
ParseDate(String, InputFormat, Locale)
```

## Exemplos

```ampscript
%%[
  SET @today = Now()
  SET @tomorrow = DateAdd(@today, 1, "D")
  SET @nextWeek = DateAdd(@today, 7, "D")
  SET @oneMonthAgo = DateAdd(@today, -1, "M")
  
  SET @formatted = FormatDate(@today, "dd/MM/yyyy", "", "pt-BR")
  SET @monthName = FormatDate(@today, "MMMM", "", "pt-BR")
  
  SET @daysUntil = DateDiff(@today, @eventDate, "D")
]%%
```

```ampscript
%%[
  SET @birthDate = ParseDate("1990-05-15", "yyyy-MM-dd")
  SET @age = DateDiff(@birthDate, @today, "Y")
]%%
```
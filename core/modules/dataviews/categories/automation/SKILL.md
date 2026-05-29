---
name: sfmc-dataviews-automation
description: Automation Studio System DataViews.
---

# DataViews - Automation

## _Automation
Automations do sistema.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| AutomationID | string | ID único |
| Name | string | Nome da automation |
| Description | string | Descrição |
| Status | int | Status numérico |
| StatusName | string | Descrição do status |
| CreatedDate | datetime | Data de criação |
| ModifiedDate | datetime | Data de modificação |
| CreatedBy | string | ID do criador |

**Status:**
- -1: Error
- 0: New
- 1: Active
- 2: Paused
- 3: Off
- 4: Scheduled

## _AutomationInstance
Instâncias executadas.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| AutomationID | string | ID da automation |
| InstanceID | string | ID da instância |
| CustomerKey | string | Chave do cliente |
| Status | int | Status da instância |
| StatusName | string | Descrição do status |
| StartTime | datetime | Início da execução |
| EndTime | datetime | Fim da execução |
| ErrorMessage | string | Mensagem de erro (se falhou) |

## _Activity
Activities dentro de automations.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| ActivityID | string | ID da activity |
| AutomationID | string | ID da automation |
| ActivityName | string | Nome |
| ActivityType | string | Tipo |
| ActivityOrder | int | Ordem |

## Query de Automation Status

```sql
SELECT 
    a.Name,
    a.StatusName,
    i.StartTime,
    i.EndTime,
    i.StatusName,
    i.ErrorMessage
FROM _Automation a
LEFT JOIN _AutomationInstance i ON a.AutomationID = i.AutomationID
WHERE a.Name LIKE '%Daily%'
ORDER BY i.StartTime DESC
```
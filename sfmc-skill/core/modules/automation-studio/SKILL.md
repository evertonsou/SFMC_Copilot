---
name: sfmc-automation-studio
description: Automation Studio activities, scheduling, triggers, and common patterns.
---

# SFMC Automation Studio Module

Automação de tarefas e workflows.

## Activity Types

| Activity | Description |
|----------|-------------|
| Query | SQL query para Data Extension |
| Filter | Segmentação por filtros |
| Import | Importar arquivo para DE |
| Export | Exportar DE para arquivo |
| Send Email | Envio de email (single or batch) |
| Send SMS | Envio de SMS |
| Fire Campaign | Dispara campanha |
| Script | SSJS custom |

## Query Activity

```sql
-- Target: Nova Data Extension
-- Action: Overwrite

SELECT 
    SubscriberKey,
    EmailAddress,
    GETDATE() AS ExtractDate
FROM _Subscribers
WHERE Status = 'Active'
```

## Filter Activity

```
Source: Data Extension
Conditions:
  - Status EQUALS Active
  - CreatedDate GREATER THAN 2024-01-01

Output: Filtered Data Extension
```

## Scheduling

| Type | Use |
|------|-----|
| Once | Execução única |
| Daily | Todo dia |
| Weekly | Dias específicos |
| Monthly | Dias do mês |
| Hourly | A cada X horas |

### Cron-like Options
```
0 0 * * *    - Daily at midnight
0 0 * * 1-5  - Weekdays at midnight
0 0 1 * *    - First day of month
```

## Trigger Types

| Trigger | Description |
|---------|-------------|
| Scheduled | Baseado em schedule |
| File Drop | Quando arquivo chega |
| Event | Baseado em API event |
| Audience | Lista atualizada |

## Common Patterns

### Daily Data Refresh
```
Schedule: Daily 6:00 AM
1. Query Activity (Refresh DE)
2. Filter Activity (Segment)
3. Import Activity (Update additional data)
```

### Send Automation
```
Schedule: Daily 10:00 AM
1. Query Activity (Audience)
2. Send Email Activity
3. Wait (1 hour)
4. Query Activity (Engagement check)
5. Decision Split
   - Opened: → Send Email 2
   - Not Opened: → Send SMS reminder
```

## Data Extract Activity

| Type | Description |
|------|-------------|
| Data Extension Extract | Export DE to FTP |
| Aggregated Data Extract | Summary reports |
| Tracking Extract | Send/Open/Click data |

## SSJS Activity

```javascript
<script runat="server">
Platform.Load("core", "1");

// Your logic here
var de = DataExtension.Init("MyDE");
var rows = de.Rows.Retrieve();

// Process rows
for (var i = 0; i < rows.length; i++) {
  // Do something
}
</script>
```

## Error Handling

- Automation continua se activity falhar
- Configure "On Error" behavior por activity
- View error logs em Activities > History
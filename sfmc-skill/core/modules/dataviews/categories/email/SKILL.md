---
name: sfmc-dataviews-email
description: Email-related System DataViews in Salesforce Marketing Cloud.
---

# DataViews - Email

## _Sent
Registros de todos os emails enviados.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador único do contato |
| JobID | int | ID do job de envio |
| ListID | int | ID da lista |
| BatchID | int | ID do batch |
| EventDate | datetime | Data/hora do envio |
| TriggererSendDefinitionObjectID | string | ID da definição de envio |
| TriggererSendDefinitionNames | string | Nome da definição |

## _Open
Registros de aberturas.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador do contato |
| JobID | int | ID do job |
| ListID | int | ID da lista |
| EventDate | datetime | Data/hora da abertura |
| IsUnique | bool | Primeira abertura (true) ou subsequente |
| Domain | string | Domínio do email |

## _Click
Registros de cliques em links.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador do contato |
| JobID | int | ID do job |
| ListID | int | ID da lista |
| EventDate | datetime | Data/hora do clique |
| URLID | int | ID da URL |
| URL | string | URL clicada |
| LinkName | string | Nome do link (alias) |
| IsUnique | bool | Primeiro clique ou não |

## _Bounce
Registros de bounces.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador do contato |
| JobID | int | ID do job |
| ListID | int | ID da lista |
| EventDate | datetime | Data/hora do bounce |
| BounceTypeID | int | Tipo de bounce |
| BounceType | string | Descrição do tipo |
| BounceCategoryID | int | Categoria do bounce |
| BounceCategory | string | Descrição da categoria |
| SMTPBounceReason | string | Razão SMTP |

**Bounce Categories:**
- 1: Hard bounce
- 2: Soft bounce

## _Unsubs
Registros de descadastros.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador do contato |
| JobID | int | ID do job (null se via Profile Center) |
| ListID | int | ID da lista |
| EventDate | datetime | Data/hora do unsubscribe |
| Status | string | Status do unsubscribe |

## _Forward
Registros de forwards.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Quem enviou |
| JobID | int | ID do job |
| EventDate | datetime | Data/hora |
| ForwardedEmail | string | Email para qual foi enviado |
| Action | string | Ação (forward) |

## _Survey
Respostas de pesquisas (se configurado).

| Campo | Tipo | Descrição |
|-------|------|-----------|
| SubscriberKey | string | Identificador |
| SurveyID | int | ID da pesquisa |
| Answer | string | Resposta |
| AnswerDate | datetime | Data da resposta |

## Query de Tracking Completo

```sql
SELECT 
    s.SubscriberKey,
    s.JobID,
    s.EventDate AS SendDate,
    o.EventDate AS OpenDate,
    o.IsUnique AS IsFirstOpen,
    c.EventDate AS ClickDate,
    c.URL,
    b.BounceType,
    b.BounceCategory,
    u.EventDate AS UnsubscribeDate
FROM _Sent s
LEFT JOIN _Open o ON s.SubscriberKey = o.SubscriberKey AND s.JobID = o.JobID
LEFT JOIN _Click c ON s.SubscriberKey = c.SubscriberKey AND s.JobID = c.JobID
LEFT JOIN _Bounce b ON s.SubscriberKey = b.SubscriberKey AND s.JobID = b.JobID
LEFT JOIN _Unsubs u ON s.SubscriberKey = u.SubscriberKey AND s.JobID = u.JobID
WHERE s.SubscriberKey = '%%email%%'
ORDER BY s.EventDate DESC
```
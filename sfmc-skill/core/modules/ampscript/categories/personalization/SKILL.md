---
name: sfmc-ampscript-personalization
description: AmpScript personalization and content handling functions.
---

# AmpScript - Personalization Functions

## Funções de Personalização

### TreatAsContent
Interpreta string como código AMPscript.

```ampscript
TreatAsContent(String)
TreatAsContent(@ampScriptBlock)
```

### TreatAsInlineContent
Interpreta e substitui na string atual.

```ampscript
TreatAsInlineContent(String)
```

### ContentArea
Insere uma Content Area.

```ampscript
ContentArea(ContentAreaID)
ContentArea(ContentAreaKey)
```

### ContentAreaByName
Insere por nome.

```ampscript
ContentAreaByName("my:folder:contentArea")
```

### AMPscript em Strings
Use `Concat` com inline AMPscript:

```ampscript
%%[
  SET @block = Concat('Hello ', @firstName, '!')
]%%
```

## Variáveis Pré-definidas

| Variável | Descrição |
|----------|-----------|
| `_subscriberKey` | Subscriber Key do contato |
| `_JobID` | ID do job de envio |
| `_ListID` | ID da lista |
| `_BatchID` | ID do batch |
| `_CampaignID` | ID da campanha |
| `_ProfileCenterURL` | URL do Profile Center |
| `view_email_url` | URL para visualizar online |
| `member_center_url` | URL do Member Center |

## Exemplos

```ampscript
%%[
  SET @customGreeting = Concat("Dear ", @firstName, ",")
  SET @dynamicContent = TreatAsContent(@ampScriptBlock)
]%%
```

```ampscript
%%[
  SET @footer = ContentArea(12345)
]%%
```
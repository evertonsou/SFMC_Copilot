---
name: sfmc-content-builder
description: Content Builder assets, templates, folders, and content blocks.
---

# SFMC Content Builder Module

Gestão de ativos e conteúdo no Content Builder.

## Asset Types

| Type | ID | Description |
|------|-----|-------------|
| htmlemail | 207 | Email HTML |
| textonlyemail | 206 | Email texto |
| template | 200 | Template |
| text | 220 | Texto |
| image | 26 | Imagem |
| file | 21 | Arquivo genérico |
| snippet | 261 | Content snippet |
| app | 318 | App block |

## Folder Structure

```
/
├── Templates/
│   ├── Email/
│   └── Landing Page/
├── Images/
├── Snippets/
└── Campaigns/
    └── 2024/
        └── Q1/
```

## Content Block Types

| Block | Description |
|-------|-------------|
| Free Form | HTML livre |
| Dynamic Content | Conteúdo por segment |
| AMPscript | Bloco com código |
| Image | Imagem com tracking |
| Button | CTA button |
| Social | Links sociais |
| Footer | Rodapé com unsubscribe |
| Header | Cabeçalho |

## REST API - Assets

### Get Assets
```javascript
GET /asset/v1/content/assets?$page=1&$pagesize=50
```

### Create Asset
```javascript
POST /asset/v1/content/assets
{
  "name": "My Email",
  "assetType": { "name": "htmlemail", "id": 207 },
  "views": {
    "html": { "content": "<html><body>Hello</body></html>" },
    "subjectline": { "content": "Subject" },
    "previewText": { "content": "Preview" }
  },
  "folder": { "id": 12345 }
}
```

### Update Asset
```javascript
PUT /asset/v1/content/assets/{id}
```

### Delete Asset
```javascript
DELETE /asset/v1/content/assets/{id}
```

## Templates

### Template Structure
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>%%=Subject=%%</title>
</head>
<body>
  %%=ContentArea("header")=%%
  %%=ContentArea("body")=%%
  %%=ContentArea("footer")=%%
</body>
</html>
```

## Content Areas

- Reutilizáveis em múltiplos emails
- Podem conter AMPscript
- Tracking automático de uso
-versioning

## Best Practices

1. Organize em pastas por campanha/tipo
2. Use content snippets para elementos reutilizáveis
3. Templates para padronização
4.命名ação consistente: `Campaign_Type_Date`
5. Delete assets não utilizados
---
name: sfmc-html-email
description: HTML email development for Salesforce Marketing Cloud. Responsive design, email clients compatibility, Outlook fixes, dark mode, and AMP for Email.
---

# SFMC HTML Email Module

Desenvolvimento de emails HTML compatíveis com todos os clientes de email.

## Visão Geral

| Tópico | Descrição |
|--------|-----------|
| Table-based Layout | Estrutura com tables (obrigatório para Outlook) |
| CSS Inline | Todos os estilos inline (obrigatório) |
| MSO Conditionals | Fixes específicos para Outlook |
| Responsive | Mobile-first, media queries |
| Dark Mode | Suporte a dark mode |
| Email Clients | Outlook, Gmail, Apple Mail, Yahoo, etc. |

## Carregamento de Sub-módulos

- `html-email/templates` - Templates starter
- `html-email/components` - Componentes (header, footer, CTA, etc.)
- `html-email/outlook-classic` - Fixes Outlook Windows
- `html-email/outlook-365` - Fixes Outlook 365
- `html-email/gmail` - Fixes Gmail
- `html-email/apple-mail` - Fixes Apple Mail
- `html-email/dark-mode` - Suporte dark mode

## Estrutura Base

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Email</title>
  <!--[if mso]>
  <noscript>
    <xml>
      <o:OfficeDocumentSettings>
        <o:PixelsPerInch>96</o:PixelsPerInch>
      </o:OfficeDocumentSettings>
    </xml>
  </noscript>
  <![endif]-->
  <style>
    /* Mobile first */
    body { margin: 0; padding: 0; background-color: #F7F7F7; }
    .container { width: 600px; max-width: 100%; }
    
    /* Desktop */
    @media only screen and (min-width: 601px) {
      .container { width: 600px !important; }
    }
    
    /* Mobile */
    @media only screen and (max-width: 480px) {
      .container { width: 100% !important; }
      .stack { display: block !important; width: 100% !important; }
    }
  </style>
</head>
<body style="margin: 0; padding: 0; background-color: #F7F7F7;">
  <table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
    <tr>
      <td align="center" style="padding: 20px 0;">
        <table class="container" role="presentation" cellpadding="0" cellspacing="0" width="600" style="background-color: #FFFFFF;">
          <!-- Content -->
        </table>
      </td>
    </tr>
  </table>
</body>
</html>
```
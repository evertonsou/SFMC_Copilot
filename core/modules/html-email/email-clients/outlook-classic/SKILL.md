---
name: sfmc-html-email-outlook-classic
description: Microsoft Outlook (Windows) specific rendering fixes and MSO conditionals.
---

# HTML Email - Outlook Classic (Windows)

Outlook usa o motor de renderização do Microsoft Word. Isso significa suporte limitado a CSS.

## Problemas Comuns

| Problema | Solução |
|----------|---------|
| `margin` não funciona | Use `padding` na `<td>` |
| `border-radius` ignorado | Use VML para botões arredondados |
| `max-width` ignorado | Use larguras fixas com MSO conditionals |
| `background-image` limitado | Use VML `<v:fill>` |
| `flexbox/grid` | Não suportado |

## MSO Conditionals

### Target Outlook Only
```html
<!--[if mso]>
<style type="text/css">
  /* Outlook-specific styles */
  .outlook-fix { width: 600px !important; }
</style>
<![endif]-->
```

### Layout com Fallback
```html
<!--[if (gte mso 9)|(IE)]>
<table cellpadding="0" cellspacing="0" border="0" width="600" align="center">
<tr><td>
<![endif]-->
<!-- Content for all clients -->
<table class="container" role="presentation" cellpadding="0" cellspacing="0" width="600" align="center">
  <tr><td>Content</td></tr>
</table>
<!--[if (gte mso 9)|(IE)]>
</td></tr></table>
<![endif]-->
```

### Target Specific Versions
```html
<!--[if mso 16]>
<!-- Outlook 2016 only -->
<![endif]-->

<!--[if gte mso 15]>
<!-- Outlook 2016/365 -->
<![endif]-->
```

## VML - Bulletproof Button

```html
<!--[if mso]>
<table border="0" cellpadding="0" cellspacing="0" role="presentation">
<tr>
<td align="center" style="padding: 15px 25px; background-color: #00AA9A; border-radius: 8px;">
<v:roundrect xmlns:v="urn:schemas-microsoft-com:vml" xmlns:w="urn:schemas-microsoft-com:office:word" href="%%=CloudPagesURL(12345)=%%" style="height: 46px; v-text-anchor: middle; width: 200px;" arcsize="15%" fillcolor="#00AA9A" strokecolor="#00AA9A">
<w:anchorlock/>
<center style="color: #ffffff; font-family: Calibri, Arial, sans-serif; font-size: 16px; font-weight: bold;">SAIBA MAIS</center>
</v:roundrect>
</td>
</tr>
</table>
<![endif]-->
<!--[if !mso]><!-->
<a href="%%=CloudPagesURL(12345)=%%" alias="CTA" target="_blank" style="display: inline-block; padding: 15px 25px; background-color: #00AA9A; color: #ffffff; font-family: Calibri, Arial, sans-serif; font-size: 16px; font-weight: bold; text-decoration: none; border-radius: 8px;">SAIBA MAIS</a>
<!--<![endif]-->
```

## VML Background Image

```html
<!--[if mso]>
<table border="0" cellpadding="0" cellspacing="0" role="presentation">
<tr>
<td valign="middle" align="center" background="https://example.com/bg.jpg" style="background-image: url('https://example.com/bg.jpg'); background-repeat: no-repeat; background-position: center; background-size: cover;">
<![endif]-->
<!-- Non-Outlook content -->
<div style="background-image: url('https://example.com/bg.jpg'); background-repeat: no-repeat; background-position: center; background-size: cover;">
  Content here
</div>
<!--[if mso]>
</td>
</tr>
</table>
<![endif]-->
```

## Tabela Fixa para Outlook

```html
<table role="presentation" cellpadding="0" cellspacing="0" border="0" width="600" align="center" style="table-layout: fixed;">
  <tr>
    <td width="600" valign="top">
      <!-- Content -->
    </td>
  </tr>
</table>
```

## Padding Fix

Outlook ignora `box-sizing: border-box`. Use padding na td:

```html
<!--[if mso]>
<td style="padding: 20px; mso-padding-alt: 20px;">
<![endif]-->
<td style="padding: 20px;">
  Content
</td>
<!--[if mso]>
</td>
<![endif]-->
```
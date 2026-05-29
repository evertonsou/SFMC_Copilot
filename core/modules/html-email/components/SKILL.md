---
name: sfmc-html-email-components
description: HTML Email components - Pre-header, Header, Footer, CTA, Spacers, Dividers, Tracking.
---

# HTML Email - Componentes

## Pre-Header

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr>
    <td align="center">
      <table class="container" role="presentation" cellpadding="0" cellspacing="0" width="600" align="center">
        <tr>
          <td align="right" style="font-family: Calibri, Arial, sans-serif; font-size: 11px; color: #696969; padding: 10px 25px 5px 0;">
            <a href="%%view_email_url%%" alias="Web Version" target="_blank" style="color: #696969; text-decoration: none;">Ver online</a>
          </td>
        </tr>
        <tr>
          <td align="center" style="font-family: Calibri, Arial, sans-serif; font-size: 11px; color: #696969; padding: 0 25px 20px 25px; line-height: 1.4;">
            <b>Importante:</b> Gentileza não responder este e-mail pois se trata de mensagem automática.
          </td>
        </tr>
      </table>
    </td>
  </tr>
</table>
```

## Tracking de Abertura (OBRIGATÓRIO)

O pixel de tracking de abertura é obrigatório para rastrear quem abriu o email. Deve ser inserido antes do `</body>`.

```html
<!-- Tracking de Abertura - OBRIGATÓRIO -->
<custom name="opencounter" type="tracking" />
```

**Versão com fallback para MSO (Outlook):**
```html
<!--[if mso]>
<custom name="opencounter" type="tracking" />
<![endif]-->
<!--[if !mso]><!-- -->
<custom name="opencounter" type="tracking" />
<!--<![endif]-->
```

**Exemplo completo em tabela:**
```html
<table role="presentation" cellpadding="0" cellspacing="0" border="0" width="100%">
  <tr>
    <td style="font-size:0; line-height:0; mso-line-height-rule: exactly;" height="1">
      <custom name="opencounter" type="tracking" />
    </td>
  </tr>
</table>
```

## Footer com Tracking de Unsubscribe (OPCIONAL)

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0" style="background-color: #F0F2F5;">
  <tr>
    <td align="center" style="padding: 30px 25px;">
      <p style="font-family: Calibri, Arial, sans-serif; font-size: 12px; color: #696969; line-height: 1.5; margin: 0;">
        Você está recebendo este email porque se cadastrou em nossa lista.<br>
        <a href="%%profile_center_url%%" alias="Profile Center" target="_blank" style="color: #00AA9A;">Atualizar preferências</a> |
        <a href="%%unsubscribe_center_url%%" alias="Unsubscribe" target="_blank" style="color: #00AA9A;">Descadastrar</a>
      </p>
      <p style="font-family: Calibri, Arial, sans-serif; font-size: 11px; color: #999999; margin: 15px 0 0 0;">
        © 2026 Empresa. Todos os direitos reservados.<br>
        CNPJ: 00.000.000/0001-00
      </p>
    </td>
  </tr>
</table>
```

## Links de Profile/Unsubscribe (OPCIONAL)

```html
<!-- Profile Center -->
<a href="%%profile_center_url%%" alias="Update Profile" target="_blank" style="color: #00AA9A;">Atualizar dados</a>

<!-- Unsubscribe Center -->
<a href="%%unsubscribe_center_url%%" alias="Unsubscribe" target="_blank" style="color: #00AA9A;">Descadastrar</a>

<!-- View in Browser -->
<a href="%%view_email_url%%" alias="View in Browser" target="_blank" style="color: #00AA9A;">Ver online</a>
```

## Campos de Endereço (OPCIONAL)

O Marketing Cloud possui campos de sistema para endereço que podem ser usados em AMPscript:

### Campos de Sistema (via API/Script)

| Campo | Descrição |
|-------|-----------|
| `Address` | Campo composto com todas as informações (disponível via API) |
| `Street` | Rua, número e complemento |
| `City` | Cidade |
| `State` | Estado ou província |
| `PostalCode` | CEP ou código postal |
| `Country` | País |

### Uso em AMPscript

```ampscript
%%[
  SET @street = [Street]
  SET @city = [City]
  SET @state = [State]
  SET @postalCode = [PostalCode]
  SET @country = [Country]
  SET @fullAddress = [Address]
]%%
```

### Exemplo em Email

```html
<td style="font-family: Calibri, Arial, sans-serif; font-size: 12px; color: #696969; padding: 20px 25px;">
  <p style="margin: 0 0 5px 0;">
    <strong>Endereço de entrega:</strong>
  </p>
  <p style="margin: 0;">
    %%Street%%<br>
    %%City%% - %%State%%<br>
    CEP: %%PostalCode%%<br>
    %%Country%%
  </p>
</td>
```

### Endereço Formatado (via AMPscript)

```ampscript
%%[
  SET @addressBlock = Concat(
    [Street], "<br>",
    [City], " - ", [State], "<br>",
    "CEP: ", [PostalCode], "<br>",
    [Country]
  )
]%%
<td style="font-family: Calibri, Arial, sans-serif; font-size: 12px; color: #696969; padding: 20px 25px;">
  %%=v(@addressBlock)=%%
</td>
```

### Endereço Estático (hardcoded)

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr>
    <td style="font-family: Calibri, Arial, sans-serif; font-size: 12px; color: #696969; padding: 20px 25px;">
      <p style="margin: 0 0 10px 0;">
        <strong>Empresa XYZ Ltda</strong><br>
        Rua Example, 123 - Bairro<br>
        CEP: 12345-678 - Cidade/UF<br>
        CNPJ: 00.000.000/0001-00
      </p>
    </td>
  </tr>
</table>
```

## Header com Logo

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0" style="background-color: #00AA9A;">
  <tr>
    <td align="center" style="padding: 30px 25px;">
      <img src="https://example.com/logo.png" alt="Logo" width="150" style="display: block; border: 0;">
    </td>
  </tr>
</table>
```

## Hero Image

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr>
    <td style="padding: 0;">
      <img src="https://example.com/hero.jpg" alt="Hero" width="600" style="display: block; max-width: 100%; height: auto; border: 0;">
    </td>
  </tr>
</table>
```

## CTA Button (Bulletproof)

```html
<!--[if mso]>
<table border="0" cellpadding="0" cellspacing="0" role="presentation">
<tr>
<td align="center" style="padding: 15px 25px; background-color: #00AA9A; border-radius: 8px;">
<span style="color: #ffffff; font-size: 16px; font-weight: bold;">SAIBA MAIS</span>
</td>
</tr>
</table>
<![endif]-->
<!--[if !mso]><!-->
<a href="%%=CloudPagesURL(12345)=%%" alias="CTA_Principal" target="_blank" style="display: inline-block; padding: 15px 25px; background-color: #00AA9A; color: #ffffff; font-family: Calibri, Arial, sans-serif; font-size: 16px; font-weight: bold; text-decoration: none; text-transform: uppercase; border-radius: 8px;">SAIBA MAIS</a>
<!--<![endif]-->
```

## Texto com Link

```html
<td style="font-family: Calibri, Arial, sans-serif; font-size: 16px; line-height: 1.5; color: #333333; padding: 0 25px 20px 25px;">
  Para mais detalhes, <a href="%%=CloudPagesURL(12345)=%%" alias="Link_Detalhes" target="_blank" rel="noopener" style="color: #00AA9A; font-weight: bold; text-decoration: none;">clique aqui e acesse</a>.
</td>
```

## Spacer

```html
<!-- 20px spacer -->
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr><td height="20" style="font-size: 20px; line-height: 20px;">&nbsp;</td></tr>
</table>

<!-- 40px spacer -->
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr><td height="40" style="font-size: 40px; line-height: 40px;">&nbsp;</td></tr>
</table>
```

## Divider

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr>
    <td style="padding: 20px 25px;">
      <table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
        <tr>
          <td style="border-top: 1px solid #e6e6e6; font-size: 1px; line-height: 1px;">&nbsp;</td>
        </tr>
      </table>
    </td>
  </tr>
</table>
```

## Footer Simples

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0" style="background-color: #F0F2F5;">
  <tr>
    <td align="center" style="padding: 30px 25px;">
      <p style="font-family: Calibri, Arial, sans-serif; font-size: 12px; color: #696969; line-height: 1.5; margin: 0;">
        Você está recebendo este email porque se cadastrou em nossa lista.<br>
        <a href="%%unsubscribe_center_url%%" alias="Unsubscribe" target="_blank" style="color: #00AA9A;">Descadastrar</a>
      </p>
    </td>
  </tr>
</table>
```

## Template Completo com Tracking

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>%%subjectline%%</title>
</head>
<body style="margin: 0; padding: 0; background-color: #F7F7F7;">
  <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
    <tr>
      <td align="center" style="padding: 20px 0;">
        <table class="container" role="presentation" cellpadding="0" cellspacing="0" width="600" style="background-color: #FFFFFF;">
          <!-- Header -->
          <tr><td style="background-color: #00AA9A; padding: 30px; text-align: center;">
            <img src="https://example.com/logo.png" alt="Logo" width="150">
          </td></tr>
          
          <!-- Content -->
          <tr><td style="padding: 40px 25px; font-family: Calibri, Arial, sans-serif; font-size: 16px; line-height: 1.6;">
            %%=ContentArea("main_content")=%%
          </td></tr>
          
          <!-- Footer -->
          <tr><td style="background-color: #F0F2F5; padding: 20px 25px; text-align: center; font-family: Calibri, Arial, sans-serif; font-size: 11px; color: #696969;">
            <a href="%%unsubscribe_center_url%%" alias="Unsubscribe" style="color: #00AA9A;">Descadastrar</a> |
            <a href="%%profile_center_url%%" alias="Profile" style="color: #00AA9A;">Atualizar preferências</a>
            <p style="margin: 10px 0 0 0;">© 2026 Empresa. CNPJ: 00.000.000/0001-00</p>
          </td></tr>
          
          <!-- Open Tracking Pixel - OBRIGATÓRIO -->
          <tr><td style="font-size:0; line-height:0; mso-line-height-rule: exactly;" height="1">
            <custom name="opencounter" type="tracking" />
          </td></tr>
        </table>
      </td>
    </tr>
  </table>
</body>
</html>
```
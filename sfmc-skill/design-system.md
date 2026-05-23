# Design System - SFMC Copilot

> **Nota**: Este arquivo contém todo o design system. Para usar em outro cliente, duplique a pasta `sfmc-skill` para a pasta do projeto desse cliente.

---

## 1. Princípios de Design

| Princípio | Descrição |
|-----------|-----------|
| **Simplicidade Inteligente** | Layouts limpos e objetivos, removendo o que for desnecessário |
| **Transparência e Ética** | Informação clara, acessível e honesta em cada interação |
| **Inovação em Movimento** | Uso de tecnologias modernas e estética que transmita dinamismo |
| **Proximidade Colaborativa** | Comunicação humana, empática e voltada para o relacionamento |

---

## 2. Paletas de Cores

### 2.1 Paleta Principal (Verde)

| Nome | Token | Hex | Uso |
|------|-------|-----|-----|
| Verde Principal | `brand-primary` | `#00AA9A` | CTAs, destaques |
| Verde Secundário | `brand-secondary` | `#8BC53F` | Links, elementos secundários |
| Verde Terciário | `brand-tertiary` | `#A0F2D3` | Backgrounds, bordas suaves |
| Verde Neve | `brand-surface` | `#F0F8F7` | Fundos de seção |

### 2.2 Paleta Accent (Roxo)

| Nome | Token | Hex | Uso |
|------|-------|-----|-----|
| Roxo Principal | `accent-primary` | `#7453E8` | CTAs alternativos, títulos |
| Roxo Secundário | `accent-secondary` | `#A854E8` | Elementos decorativos |
| Roxo Terciário | `accent-tertiary` | `#D6CAFF` | Backgrounds suaves |
| Roxo Neve | `accent-surface` | `#F2EFFF` | Fundos destacados |

### 2.3 Cores de Destaque

| Nome | Token | Hex | Uso |
|------|-------|-----|-----|
| Amarelo Ouro | `highlight-gold` | `#FFCF00` | Recompensas, descontos |
| Azul Profundo | `highlight-blue` | `#0071BC` | Links secundários |
| Azul Turquesa | `highlight-cyan` | `#00A0C6` | Informações, ícones |
| Azul Acinzentado | `highlight-slate` | `#6A89A7` | Elementos neutros |
| Creme | `highlight-cream` | `#FFFBEB` | Backgrounds especiais |

### 2.4 Neutros

| Nome | Token | Hex | Uso |
|------|-------|-----|-----|
| Texto Principal | `neutral-dark` | `#333333` | Textos principais |
| Texto Secundário | `neutral-medium` | `#696969` | Textos de apoio |
| Fundo Seção | `neutral-light` | `#F0F2F5` | Backgrounds de seção |
| Fundo Base | `neutral-base` | `#F7F7F7` | Background da página |

### 2.5 Cores Semânticas

| Nome | Hex | Uso |
|------|-----|-----|
| Error | `#DC2626` | Mensagens de erro |
| Success | `#059669` | Mensagens de sucesso |
| Warning | `#D97706` | Avisos |
| Info | `#2563EB` | Informações |

### 2.6 Diretrizes de Uso de Cores

1. **Paleta por Campanha**: Escolha Verde OU Roxo como base principal. Não misture.
2. **Hierarquia de Texto**: Use no máximo 2 cores no texto principal.
3. **Equilíbrio Visual**: Limite a 3 cores para elementos visuais.
4. **Foco em Conversão**: Use cores principais em CTAs e títulos.
5. **Estratégia de Alerta**: Amarelo Ouro para recompensas e descontos.

---

## 3. Tipografia

### 3.1 Famílias de Fontes

| Fonte | Fallback | Uso |
|-------|----------|-----|
| Calibri | Arial, Helvetica, sans-serif | Texto principal |
| Verdana | Tahoma, Geneva | Títulos, destaques |

### 3.2 Escala Tipográfica

| Elemento | Tamanho | Peso | Line-height | Letter-spacing |
|----------|---------|------|-------------|----------------|
| H1 | 32px | Bold | 1.2 | normal |
| H2 | 24px | Bold | 1.3 | normal |
| H3 | 20px | Bold | 1.4 | normal |
| Body | 16px | Normal | 1.5 | normal |
| Caption | 12-14px | Normal | 1.5 | normal |
| Small | 11px | Normal | 1.4 | normal |

### 3.3 Classes CSS

```css
.font-h1 { font-size: 32px; font-weight: bold; line-height: 1.2; }
.font-h2 { font-size: 24px; font-weight: bold; line-height: 1.3; }
.font-h3 { font-size: 20px; font-weight: bold; line-height: 1.4; }
.font-body { font-size: 16px; font-weight: normal; line-height: 1.5; }
.font-caption { font-size: 12px; font-weight: normal; line-height: 1.5; }
```

---

## 4. Sistema de Espaçamento (8pt Grid)

| Token | Valor | Uso |
|-------|-------|-----|
| xs | 4px | Espaçamentos mínimos |
| sm | 8px | Entre elementos próximos |
| md | 16px | Entre elementos relacionados |
| lg | 24px | Entre seções |
| xl | 32px | Espaçamentos grandes |
| 2xl | 40px | Entre blocos principais |
| 3xl | 64px | Espaçamentos de página |

### Classes CSS

```css
.spacing-xs { padding: 4px; }
.spacing-sm { padding: 8px; }
.spacing-md { padding: 16px; }
.spacing-lg { padding: 24px; }
.spacing-xl { padding: 32px; }
.spacing-2xl { padding: 40px; }
.spacing-3xl { padding: 64px; }
```

---

## 5. Componentes

### 5.1 Botões (CTAs)

#### Botão Primário (Filled)

| Propriedade | Valor |
|-------------|-------|
| Background | `#00AA9A` |
| Color | `#ffffff` |
| Border-radius | `8px` |
| Padding-vertical | `15px` |
| Padding-horizontal | `25px` |
| Font-size | `16px` |
| Font-weight | `bold` |
| Text-transform | `uppercase` |
| Letter-spacing | `0.05em` |

```html
<a href="URL" alias="CTA_Primary" target="_blank" style="display: inline-block; padding: 15px 25px; background-color: #00AA9A; color: #ffffff; font-family: Calibri, Arial, sans-serif; font-size: 16px; font-weight: bold; text-decoration: none; text-transform: uppercase; border-radius: 8px;">SAIBA MAIS</a>
```

#### Botão Secundário (Pill/Rounded)

| Propriedade | Valor |
|-------------|-------|
| Background | `#7453E8` |
| Color | `#ffffff` |
| Border-radius | `50px` (pílula) |
| Padding | `15px 25px` |
| Font-size | `16px` |
| Font-weight | `bold` |
| Text-transform | `uppercase` |

```html
<a href="URL" alias="CTA_Secondary" target="_blank" style="display: inline-block; padding: 15px 25px; background-color: #7453E8; color: #ffffff; font-family: Calibri, Arial, sans-serif; font-size: 16px; font-weight: bold; text-decoration: none; text-transform: uppercase; border-radius: 50px;">ASSINAR AGORA</a>
```

#### Botão Outline

| Propriedade | Valor |
|-------------|-------|
| Background | `transparent` |
| Color | `#00AA9A` |
| Border | `2px solid #00AA9A` |
| Border-radius | `8px` |
| Padding-vertical | `13px` |
| Padding-horizontal | `23px` |

```html
<a href="URL" alias="CTA_Outline" target="_blank" style="display: inline-block; padding: 13px 23px; background-color: transparent; color: #00AA9A; font-family: Calibri, Arial, sans-serif; font-size: 16px; font-weight: bold; text-decoration: none; border-radius: 8px; border: 2px solid #00AA9A;">VER DETALHES</a>
```

#### Botão de Texto (Text Link)

| Propriedade | Valor |
|-------------|-------|
| Background | `transparent` |
| Color | `#00AA9A` |
| Border | `none` |
| Font-size | `14px` |
| Font-weight | `bold` |
| Text-decoration | `underline` |

```html
<a href="URL" alias="CTA_Text" target="_blank" style="color: #00AA9A; font-weight: bold; text-decoration: underline;">Saiba mais →</a>
```

#### Touch Target

| Propriedade | Valor |
|-------------|-------|
| Min-width | `44px` |
| Min-height | `44px` |

### 5.2 Border Radius

| Nome | Valor | Uso |
|------|-------|-----|
| Small | `4px` | Botões pequenos |
| Medium | `8px` | Botões padrão |
| Large | `16px` | Cards, containers |
| Full | `50px` | Botões pílula |

### 5.3 Shadows

| Nome | Valor | Uso |
|------|-------|-----|
| sm | `0 1px 2px rgba(0,0,0,0.05)` | Elementos sutis |
| md | `0 4px 6px rgba(0,0,0,0.1)` | Cards |
| lg | `0 10px 15px rgba(0,0,0,0.1)` | Modais, dropdowns |

### 5.4 Cards

| Propriedade | Valor |
|-------------|-------|
| Background | `#FFFFFF` |
| Border-radius | `12px` |
| Border | `1px solid #e5e7eb` |
| Padding | `24px` |

### 5.5 Divisores

| Propriedade | Valor |
|-------------|-------|
| Border-color | `#e6e6e6` |
| Border-width | `1px` |
| Style | `solid` |

---

## 6. Layout de Email

### 6.1 Container Principal

| Propriedade | Valor |
|-------------|-------|
| Max-width | `600px` |
| Padding-horizontal | `25px` |
| Padding-vertical | `20px` |

### 6.2 Estrutura Base

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" style="max-width: 600px; margin: 0 auto;">
  <tr>
    <td>
      <!-- Content -->
    </td>
  </tr>
</table>
```

### 6.3 Responsividade

```css
/* Desktop */
.container { width: 600px; }

/* Mobile */
@media only screen and (max-width: 480px) {
  .container { width: 100% !important; max-width: 100% !important; }
  .fluid-img { width: 100% !important; height: auto !important; }
  .stack-col { display: block !important; width: 100% !important; }
}
```

---

## 7. Componentes de Email

### 7.1 Pre-Header

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
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
```

### 7.2 Spacers

```html
<!-- 20px -->
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr><td height="20" style="font-size: 20px; line-height: 20px;">&nbsp;</td></tr>
</table>

<!-- 40px -->
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0">
  <tr><td height="40" style="font-size: 40px; line-height: 40px;">&nbsp;</td></tr>
</table>
```

### 7.3 Footer

```html
<table role="presentation" cellpadding="0" cellspacing="0" width="100%" border="0" style="background-color: #F0F2F5;">
  <tr>
    <td align="center" style="padding: 30px 25px;">
      <p style="font-family: Calibri, Arial, sans-serif; font-size: 12px; color: #696969; line-height: 1.5; margin: 0;">
        Você está recibiendo este email porque se cadastrou.<br>
        <a href="%%unsubscribe_center_url%%" alias="Unsubscribe" target="_blank" style="color: #00AA9A;">Descadastrar</a>
      </p>
      <p style="font-family: Calibri, Arial, sans-serif; font-size: 11px; color: #999999; margin: 15px 0 0 0;">
        © 2026 Empresa. CNPJ: 00.000.000/0001-00
      </p>
    </td>
  </tr>
</table>
```

---

## 8. Diretrizes de Implementação

### 8.1 Checklist de Email

- [ ] Usar web-safe fonts (Calibri, Arial, Helvetica)
- [ ] Max-width 600px para container
- [ ] CSS inline em todos os elementos
- [ ] Tracking de abertura (`<custom name="opencounter" type="tracking" />`)
- [ ] Alt text em todas as imagens
- [ ] Links com `alias` para tracking
- [ ] `target="_blank"` em todos os links externos
- [ ] Touch target mínimo 44x44px
- [ ] Testar em Outlook e mobile

### 8.2 Checklist de Landing Pages

- [ ] Form validation server-side
- [ ] HTTPS (CloudPages já oferece)
- [ ] Sanitização de inputs
- [ ] CSRF protection se necessário
- [ ] Mobile responsive
- [ ] Tracking de conversões

---

## 9. Tokens AMPscript

### 9.1 Personalização

```ampscript
%%FIRSTNAME%%      %%LASTNAME%%      %%EMAIL%%
%%SUBSCRIBERKEY%%  %%JobID%%         %%ListID%%
```

### 9.2 Sistema

```ampscript
%%view_email_url%%           %%profile_center_url%%
%%unsubscribe_center_url%%   %%member_center_url%%
```

### 9.3 Campos de Endereço

```ampscript
%%Street%%    %%City%%    %%State%%
%%PostalCode%% %%Country%% %%Address%%
```

### 9.4 Tracking

```ampscript
<custom name="opencounter" type="tracking" />
```

---

*Este documento é o design system oficial. Para duplicar para outro cliente, copie toda a pasta `sfmc-skill`.*
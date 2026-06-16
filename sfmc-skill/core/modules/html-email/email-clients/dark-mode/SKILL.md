---
name: sfmc-html-email-dark-mode
description: Dark mode email rendering support for all email clients.
---

# HTML Email - Dark Mode

Mais de 40% dos subscribers visualizam emails em dark mode.

## Estratégias

### Meta Tags (Obligatórias)
```html
<head>
  <meta name="color-scheme" content="light dark">
  <meta name="supported-color-schemes" content="light dark">
</head>
```

## Apple Mail Dark Mode

```css
@media (prefers-color-scheme: dark) {
  body, .email-body { background-color: #1a1a1a !important; color: #f0f0f0 !important; }
  .header { background-color: #2d2d2d !important; }
  .content { background-color: #1a1a1a !important; color: #f0f0f0 !important; }
  .btn-primary { background-color: #00AA9A !important; }
  .text-primary { color: #f0f0f0 !important; }
  .text-secondary { color: #b0b0b0 !important; }
  img.dark-mode-hide { display: none; }
  img.light-mode-hide { display: block !important; }
  a { color: #4ECDC4 !important; }
}
```

## Outlook.com / Office 365

```css
[data-ogsc] body { background-color: #1a1a1a !important; color: #f0f0f0 !important; }
[data-ogsc] .header { background-color: #2d2d2d !important; }
[data-ogsc] .content { background-color: #1a1a1a !important; color: #f0f0f0 !important; }
[data-ogsc] .btn-primary { background-color: #00AA9A !important; }
[data-ogsc] .text-primary { color: #f0f0f0 !important; }
[data-ogsc] a { color: #4ECDC4 !important; }

[data-ogsb] .header { background-color: #2d2d2d !important; }
[data-ogsb] .content { background-color: #1a1a1a !important; }
```

## Gmail Dark Mode

Gmail inverte automaticamente cores claras. Use cores escuras explícitas:

```css
@media (prefers-color-scheme: dark) {
  .email-body { background-color: #121212 !important; }
  .content-box { background-color: #1E1E1E !important; color: #E0E0E0 !important; }
}
```

## Imagens para Dark Mode

### Versão Alternativa
```html
<img src="logo-light.png" alt="Logo" class="dark-mode-hide" style="display: block;">
<img src="logo-dark.png" alt="Logo" class="light-mode-hide" style="display: none;">
```

### Dicas
- Use PNGs com background transparente
- Adicione stroke/glow em ícones claros
- Evite texto dentro de imagens

## Cores Seguras para Dark Mode

| Light Mode | Dark Mode |
|------------|-----------|
| #FFFFFF (branco) | #FEFEFE (off-white) |
| #000000 (preto) | #0E0E0E (off-black) |
| #F7F7F7 (fundo) | #121212 (fundo escuro) |
| #333333 (texto) | #E0E0E0 (texto claro) |

## Template com Dark Mode

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="color-scheme" content="light dark">
  <meta name="supported-color-schemes" content="light dark">
  <style>
    body { background-color: #F7F7F7; color: #333333; margin: 0; padding: 0; }
    .header { background-color: #00AA9A; color: #ffffff; }
    .content { background-color: #FFFFFF; color: #333333; }
    
    @media (prefers-color-scheme: dark) {
      body { background-color: #121212 !important; color: #E0E0E0 !important; }
      .header { background-color: #008577 !important; }
      .content { background-color: #1E1E1E !important; color: #E0E0E0 !important; }
      img.dark-mode-hide { display: none; }
    }
    
    [data-ogsc] .header { background-color: #008577 !important; }
    [data-ogsc] .content { background-color: #1E1E1E !important; color: #E0E0E0 !important; }
  </style>
</head>
<body>
  <!-- Email content -->
</body>
</html>
```
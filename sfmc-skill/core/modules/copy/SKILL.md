---
name: sfmc-copy
description: Copywriting, CTAs, KPIs and best practices for Salesforce Marketing Cloud email marketing.
---

# SFMC Copy, CTAs e KPIs

## Copywriting

### Princípios

| Princípio | Descrição |
|-----------|-----------|
| Conversational | Escreva como fala |
| Brevidade | Um tema por email |
| Valor | Cada email deve trazer valor |
| Clareza | Evite jargões |

### Linha de Assunto

| Regra | Recomendação |
|-------|-------------|
| Comprimento | < 60 caracteres (9 palavras) |
| Urgência | "Hoje", "Último dia", "Agora" |
| Personalização | Use FIRSTNAME com cuidado |
| Evitar | ALL CAPS, !!!, Spam triggers |

**Exemplos:**
- ✅ "John, sua fatia está quase pronta 🍕"
- ❌ "PROMOÇÃO!!! DESCONTOS IMPERDÍVEIS!!!"

### Preview Text

- Complementa o subject line
- 40-130 caracteres
- Não repita o subject
- Adicione contexto

### Body Copy

```html
<!-- Bom: Conversacional e claro -->
<td style="font-family: Calibri; font-size: 16px; line-height: 1.5; color: #333;">
  <p>Olá, João!</p>
  <p>Você sabia que podeкономисать até 30% na sua conta de luz?</p>
  <p>É simples: basta trocar seu plano para o nosso Essencial.</p>
</td>
```

### Boas Práticas

1. **Headline forte** - Primeiro parágrafo deve capturar atenção
2. **Benefícios** - Foque em benefícios, não features
3. **CTA claro** - Um primary CTA por email
4. **Escaneável** - Use parágrafos curtos, bullet points
5. **Proofread** - Leia em voz alta, corrija erros

## CTAs (Call-to-Action)

### Anatomia do CTA

| Elemento | Recomendação |
|----------|--------------|
| Lingua | Verbos de ação ("Shop", "Get", "Start") |
| Tamanho | Mínimo 44x44px touch target |
| Cor | Contraste alto com background |
| Espaço | Whitespace ao redor |
| Posição | Acima do fold + repeat |

### Tipos de CTA

```html
<!-- Primary Button (Filled) -->
<a href="%%=CloudPagesURL(12345)=%%" alias="CTA_Primary" 
   style="display: inline-block; padding: 15px 25px; background-color: #00AA9A; 
          color: #ffffff; font-family: Calibri; font-size: 16px; font-weight: bold; 
          text-decoration: none; border-radius: 8px;">SAIBA MAIS</a>

<!-- Secondary Button (Outline) -->
<a href="%%=CloudPagesURL(12345)=%%" alias="CTA_Secondary" 
   style="display: inline-block; padding: 13px 23px; background-color: transparent; 
          color: #00AA9A; font-family: Calibri; font-size: 16px; font-weight: bold; 
          text-decoration: none; border-radius: 8px; border: 2px solid #00AA9A;">
  VER DETALHES</a>

<!-- Text Link -->
<a href="%%=CloudPagesURL(12345)=%%" alias="CTA_Text" 
   style="color: #00AA9A; font-weight: bold; text-decoration: underline;">
  Saiba mais sobre o plano →</a>
```

### Copy de CTAs

| Categoria | Exemplos |
|-----------|----------|
| E-commerce | "Shop Now", "Buy Today", "Get 20% Off" |
| SaaS | "Start Free Trial", "See Plans", "Get Started" |
| Newsletter | "Read More", "Continue Reading" |
| Promocional | "Grab Your Deal", "Claim Offer" |
| Transacional | "View Order", "Track Shipment" |

### Copy Fórmulas

- **Valor**: "Get 20% off today"
- **Urgência**: "Last chance", "Expires midnight"
- **Baixo compromisso**: "Start free trial" vs "Buy now"
- **Descritivo**: "Shop fall collection" vs "Click here"

## KPIs

### Métricas de Email

| Métrica | Definição | Benchmark |
|---------|-----------|-----------|
| **Open Rate** | Abertos / Enviados | 15-25% |
| **Click-Through Rate (CTR)** | Cliques / Enviados | 2-5% |
| **Click-to-Open Rate (CTOR)** | Cliques / Abertos | 10-15% |
| **Conversion Rate** | Conversões / Enviados | Varia por indústria |
| **Unsubscribe Rate** | Unsubs / Enviados | < 0.5% |
| **Bounce Rate** | Bounces / Enviados | < 2% |
| **List Growth Rate** | (Novos - Unsubs) / Total | > 5% |

### Métricas de Engajamento

| Métrica | Descrição |
|---------|-----------|
| Read Time | Tempo de leitura |
| Forward/Share Rate | Amplificação social |
| Engagement by Device | Mobile vs Desktop vs Webmail |
| Email Client Usage | Breakdown por cliente |

### Métricas de Receita

| Métrica | Definição |
|---------|-----------|
| Revenue per Email | Receita / Emails enviados |
| ROI | (Receita - Custo) / Custo |
| AOV | Average Order Value |
| CLV | Customer Lifetime Value |
| CAC | Customer Acquisition Cost |

### Benchmarks por Indústria

| Indústria | Open Rate | CTR | CTOR |
|-----------|-----------|-----|------|
| E-commerce | 15-20% | 2-3% | 10-15% |
| Finance | 20-25% | 3-4% | 12-18% |
| SaaS | 20-25% | 3-5% | 15-20% |
| Media | 20-25% | 2-3% | 8-12% |
| Travel | 15-20% | 2-4% | 10-15% |

## A/B Testing

### O que Testar

| Elemento | Impacto |
|----------|---------|
| Subject Line | Alto - inbox first impression |
| Preview Text | Médio - complementa subject |
| CTA Copy | Alto - direto em conversão |
| CTA Color | Médio - visibilidade |
| Send Time | Alto - quando visualizado |
| From Name | Médio - confiança |

### Regras

1. **Uma variável** por teste
2. **10.000+** emails para significância
3. **95%** confiança antes de declarar vencedor
4. **48-72h** duração mínima
5. **Documente** resultados

### Exemplo de Teste

```
Test: CTA Copy
Variant A: "Shop Now" 
Variant B: "Get 20% Off Today"

Winner: Variant B (+15% CTR)
Confidence: 97%
```
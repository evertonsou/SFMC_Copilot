# Module: Einstein AI

> **Skill**: sfmc-copilot | **Categoria**: AI & Personalization | **Tokens**: ~2K

---

## Visão Geral

**Einstein** é a camada de IA nativa do Salesforce Marketing Cloud. Oferece personalização preditiva, otimização de envios e scoring de engajamento sem necessidade de modelos externos.

---

## Produtos Einstein no SFMC

| Produto | Função | Disponibilidade |
|---------|--------|-----------------|
| **Einstein Send Time Optimization (STO)** | Envia no melhor horário por contato | Todos os planos |
| **Einstein Email Recommendations** | Produtos/conteúdo personalizado por subscriber | Add-on |
| **Einstein Predictive Scoring** | Score de engajamento e churn | Add-on |
| **Einstein Copy Insights** | Análise de subject lines | Add-on |
| **Einstein Engagement Frequency** | Frequência ideal de envio | Add-on |
| **Einstein Content Tagging** | Tags automáticas em assets | Incluído no CB |

---

## Einstein Send Time Optimization (STO)

### Como Funciona
- Analisa histórico de aberturas de cada subscriber
- Calcula o horário de maior probabilidade de abertura
- Entrega o e-mail no horário ideal (dentro de uma janela de 24h)

### Ativar no Email Send

1. Email Studio → **Send** → **Schedule**
2. Ativar **Einstein Send Time Optimization**
3. Definir janela de envio (ex: 24h a partir de agora)
4. Confirmar envio

### Ativar no Journey Builder

1. Send Email Activity → **Schedule** aba
2. Ativar **Send Time Optimization**
3. Definir janela máxima de espera

### AMPscript — Verificar Horário Previsto

```ampscript
%%[
/* Exibir horário previsto de entrega no assunto */
/* Nota: Einstein calcula internamente — não exposto via AMPscript diretamente */
/* Use variáveis de contexto da jornada para logging */
SET @sendTime = AttributeValue("EinsteinSTO_ScheduledTime")
]%%
```

---

## Einstein Email Recommendations

### Como Funciona
- Analisa comportamento de navegação e compras (via tracking pixel ou API)
- Gera recomendações de produtos/conteúdo personalizadas por subscriber
- Inseridas via bloco dinâmico no email

### Configurar Catálogo de Produtos

1. **Einstein** → **Email Recommendations** → **Catalogs**
2. Importar catálogo (CSV ou feed URL):
   - `ProductID`, `Name`, `Description`, `ImageURL`, `Price`, `Category`, `URL`
3. Configurar **Zones** (áreas de recomendação no e-mail)
4. Definir algoritmo: Most Popular, Collaborative Filtering, Recently Viewed

### Inserir Recomendações no E-mail (AMPscript)

```ampscript
%%[
/* Einstein Email Recommendations — Bloco de Produtos */
SET @subscriberID = _subscriberkey

/* Chamar API de recomendações */
SET @einsteinRec = RecommendationsStream(
  "ZoneName",       /* Nome da Zone configurada */
  "EmailAddress",   emailaddr,
  5                 /* Número de recomendações */
)
]%%

%%[ FOR @i = 1 TO RowCount(@einsteinRec) DO ]%%
  %%[ SET @rec = Row(@einsteinRec, @i) ]%%
  
  <div class="product-card">
    <img src="%%=Field(@rec, 'ImageURL')=%%" alt="%%=Field(@rec, 'Name')=%%">
    <h3>%%=Field(@rec, 'Name')=%%</h3>
    <p>R$ %%=Field(@rec, 'Price')=%%</p>
    <a href="%%=Field(@rec, 'URL')=%%">Ver Produto</a>
  </div>

%%[ NEXT @i ]%%
```

### Feed de Catálogo — Estrutura

```csv
id,name,description,imageUrl,price,url,category,brand
PROD001,Camiseta Premium,Camiseta 100% algodão,https://img.com/1.jpg,89.90,https://loja.com/1,Moda,Brand X
PROD002,Calça Jeans,Jeans slim fit,https://img.com/2.jpg,179.90,https://loja.com/2,Moda,Brand Y
```

---

## Einstein Predictive Scoring

### Scores Disponíveis

| Score | Descrição | Range |
|-------|-----------|-------|
| **Email Send Score** | Probabilidade de abrir | 0–10 |
| **Email Open Score** | Tendência de engajamento | 0–10 |
| **Churn Score** | Risco de desengajamento | 0–10 |
| **Conversion Score** | Probabilidade de conversão | 0–10 |

### Acessar Scores via AMPscript

```ampscript
%%[
/* Scores ficam disponíveis como atributos após Einstein processar */
SET @emailScore   = AttributeValue("Einstein_EmailSendScore")
SET @churnScore   = AttributeValue("Einstein_ChurnScore")
SET @convScore    = AttributeValue("Einstein_ConversionScore")

/* Personalização baseada em score */
IF @churnScore > 7 THEN
  SET @mensagem = "Sentimos sua falta! Aqui está um desconto especial."
  SET @oferta   = "20%"
ELSEIF @emailScore > 8 THEN
  SET @mensagem = "Confira nossas novidades exclusivas para você!"
  SET @oferta   = "10%"
ELSE
  SET @mensagem = "Temos uma oferta especial esperando por você."
  SET @oferta   = "5%"
ENDIF
]%%

%%=v(@mensagem)=%%
<strong>Seu desconto: %%=v(@oferta)=%%</strong>
```

### SQL — Segmentação por Score Einstein

```sql
-- Subscribers com alto risco de churn para campanha de retenção
SELECT
  s.SubscriberKey,
  s.EmailAddress,
  ep.Einstein_ChurnScore,
  ep.Einstein_EmailSendScore,
  ep.LastUpdated
FROM _Subscribers s
JOIN DE_Einstein_Scores ep ON s.SubscriberKey = ep.SubscriberKey
WHERE ep.Einstein_ChurnScore >= 7
  AND s.Status = 'Active'
ORDER BY ep.Einstein_ChurnScore DESC
```

---

## Einstein Copy Insights

### Como Usar

1. Content Builder → **Criar Email** → **Subject Line**
2. Clicar em **Einstein Insights** (ícone ⚡)
3. Ver análise: tom, comprimento, palavras de impacto
4. Comparar sugestões de subject lines

### Métricas Analisadas

| Métrica | Descrição |
|---------|-----------|
| **Reading Level** | Nível de complexidade do texto |
| **Engagement** | Score previsto de abertura |
| **Sentiment** | Positivo, negativo, neutro |
| **Word Count** | Total e palavras de impacto |

---

## Einstein Engagement Frequency

### Configurar Frequência Ideal

1. **Einstein** → **Engagement Frequency**
2. Einstein analisa padrão de opens e fatigue
3. Segmenta automaticamente:
   - **High Frequency** (toleram muitos envios)
   - **Standard Frequency** (frequência normal)
   - **Low Frequency** (precisam de menos e-mails)

### Usar Segmentos de Frequência no Journey

```
Entry: Todos os Ativos

Decision Split: Qual segmento Einstein Frequency?
  ├── High → Enviar a cada 3 dias
  ├── Standard → Enviar a cada 7 dias
  └── Low → Enviar a cada 14 dias
```

---

## Einstein Analytics (Relatórios)

### Métricas Disponíveis

- **Engagement Rate por Score**: Correlação entre score Einstein e abertura real
- **Churn Prediction Accuracy**: Precisão das previsões de churn
- **STO Impact**: Comparativo de abertura com/sem STO

### Acessar Einstein Dashboard

**Analytics Builder** → **Einstein Analytics** → Selecionar relatório

---

## Boas Práticas

```
✅ Ative o STO para todos os envios — melhora abertura em média 10-15%
✅ Alimente o catálogo de produtos semanalmente para Recommendations
✅ Use Churn Score >7 para campanhas de retenção com incentivo
✅ Combine Einstein Frequency com Journey Builder para frequência adaptativa
✅ Monitore Copy Insights antes de finalizar subject lines
✅ Treine o modelo com pelo menos 6 meses de dados históricos
✅ Teste A/B: e-mails com vs sem recomendações Einstein
✅ Revise a precisão dos scores trimestralmente
```

---

## Referências

- [Einstein for Marketing Cloud](https://help.salesforce.com/s/articleView?id=sf.mc_einstein_overview.htm)
- [Send Time Optimization](https://help.salesforce.com/s/articleView?id=sf.mc_einstein_sto.htm)
- [Email Recommendations](https://help.salesforce.com/s/articleView?id=sf.mc_einstein_email_recommendations.htm)
- [Predictive Scoring](https://help.salesforce.com/s/articleView?id=sf.mc_einstein_predictive_scoring.htm)

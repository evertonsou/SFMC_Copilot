# Module: Deliverability

> **Skill**: sfmc-copilot | **Categoria**: Email Deliverability | **Tokens**: ~2.5K

---

## Visão Geral

**Deliverability** é a capacidade de chegar na **caixa de entrada** (não spam) do destinatário. É influenciada por reputação do IP, autenticação de domínio, qualidade da lista e conteúdo do e-mail.

---

## Fundamentos de Deliverability

### Fatores que Afetam a Entregabilidade

| Fator | Peso | Controle |
|-------|------|---------|
| Reputação do IP | Alto | Médio |
| Reputação do Domínio | Alto | Alto |
| Autenticação (SPF/DKIM/DMARC) | Alto | Alto |
| Qualidade da lista | Alto | Alto |
| Engajamento histórico | Médio | Médio |
| Conteúdo do e-mail | Médio | Alto |
| Taxa de bounce | Alto | Alto |
| Taxa de spam complaints | Crítico | Médio |

---

## Autenticação de E-mail

### SPF (Sender Policy Framework)

Autoriza IPs a enviar e-mail pelo seu domínio.

```dns
/* Registro TXT no DNS */
v=spf1 include:exacttarget.com include:_spf.salesforce.com ~all

/* Verificar SPF */
/* Ferramenta: mxtoolbox.com/spf.aspx */
```

### DKIM (DomainKeys Identified Mail)

Assina digitalmente os e-mails para garantir autenticidade.

```
/* Configurar no SFMC */
Admin → Domain Management → DKIM

/* O SFMC gera automaticamente as chaves */
/* Você adiciona os registros CNAME no DNS */

s1._domainkey.seudominio.com → [valor gerado pelo SFMC]
s2._domainkey.seudominio.com → [valor gerado pelo SFMC]
```

### DMARC (Domain-based Message Authentication)

Define política para e-mails que falham SPF/DKIM.

```dns
/* Registro TXT */
_dmarc.seudominio.com

/* Valor recomendado (iniciar em monitor, evoluir para quarantine/reject) */
v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@seudominio.com; pct=100
```

### Progressão DMARC Recomendada

```
Semana 1-4:   p=none    → Monitorar sem bloquear
Semana 5-8:   p=quarantine; pct=10  → Bloquear 10%
Semana 9-12:  p=quarantine; pct=100 → Bloquear todos
Semana 13+:   p=reject; pct=100     → Rejeitar no servidor
```

---

## IP Warming (Aquecimento de IP)

### Por que Aquecer?

IPs novos têm **reputação zero**. ISPs (Gmail, Outlook, Yahoo) são conservadores com IPs desconhecidos. O aquecimento constrói reputação gradualmente.

### Cronograma de Aquecimento Recomendado

| Semana | Volume Diário | Público Alvo |
|--------|---------------|--------------|
| 1 | 2.000 | Mais engajados (abriram nos últimos 30 dias) |
| 2 | 5.000 | Engajados (60 dias) |
| 3 | 10.000 | Engajados (90 dias) |
| 4 | 20.000 | Ativos (180 dias) |
| 5 | 50.000 | Base ampla |
| 6 | 100.000 | Base completa |
| 7+ | Escala | Manutenção de reputação |

### Boas Práticas de Aquecimento

```
✅ Envie sempre para os subscribers MAIS engajados primeiro
✅ Mantenha consistência — envie todos os dias
✅ Use o mesmo From Domain e IP dedicado
✅ Monitore métricas diariamente durante o aquecimento
✅ Reaja imediatamente a aumentos de bounce ou spam complaints
✅ Não interrompa o aquecimento por mais de 48h
```

### SQL — Segmentar Mais Engajados para Aquecimento

```sql
-- Top engajados: abriram pelo menos 3x nos últimos 90 dias
SELECT
  s.SubscriberKey,
  s.EmailAddress,
  COUNT(DISTINCT o.JobID) AS TotalOpens,
  MAX(o.EventDate) AS LastOpen
FROM _Subscribers s
JOIN _Open o ON s.SubscriberKey = o.SubscriberKey
WHERE o.EventDate >= DATEADD(DAY, -90, GETDATE())
  AND s.Status = 'Active'
GROUP BY s.SubscriberKey, s.EmailAddress
HAVING COUNT(DISTINCT o.JobID) >= 3
ORDER BY TotalOpens DESC
```

---

## Gerenciamento de Bounces

### Tipos de Bounce

| Tipo | Código | Descrição | Ação |
|------|--------|-----------|------|
| **Hard Bounce** | 5xx | Endereço inválido permanente | Remover imediatamente |
| **Soft Bounce** | 4xx | Problema temporário (caixa cheia, servidor off) | Retry automático |
| **Block Bounce** | - | ISP bloqueou o IP/domínio | Investigar blacklisting |
| **Technical Bounce** | - | Erro de protocolo | Verificar configurações |

### SQL — Monitor de Bounces

```sql
-- Taxa de bounce por envio nos últimos 30 dias
SELECT
  j.JobID,
  j.EmailName,
  j.SchedTime,
  COUNT(DISTINCT s.SubscriberKey) AS TotalSent,
  COUNT(DISTINCT b.SubscriberKey) AS TotalBounced,
  CAST(COUNT(DISTINCT b.SubscriberKey) AS FLOAT) / 
    NULLIF(COUNT(DISTINCT s.SubscriberKey), 0) * 100 AS BounceRate
FROM _Job j
JOIN _Sent s ON j.JobID = s.JobID
LEFT JOIN _Bounce b ON j.JobID = b.JobID AND s.SubscriberKey = b.SubscriberKey
WHERE j.SchedTime >= DATEADD(DAY, -30, GETDATE())
GROUP BY j.JobID, j.EmailName, j.SchedTime
HAVING CAST(COUNT(DISTINCT b.SubscriberKey) AS FLOAT) / 
    NULLIF(COUNT(DISTINCT s.SubscriberKey), 0) * 100 > 2  -- Alerta: >2%
ORDER BY BounceRate DESC
```

### Automation de Limpeza de Bounces

```sql
-- Identificar para remoção: 2+ hard bounces em 6 meses
INSERT INTO DE_RemovalList (EmailAddress, SubscriberKey, Reason, DetectedDate)
SELECT DISTINCT
  b.EmailAddress,
  b.SubscriberKey,
  'Hard Bounce x2+',
  GETDATE()
FROM _Bounce b
WHERE b.BounceType = 'HardBounce'
  AND b.EventDate >= DATEADD(MONTH, -6, GETDATE())
GROUP BY b.EmailAddress, b.SubscriberKey
HAVING COUNT(*) >= 2
```

---

## Spam Complaints

### Thresholds Críticos

| ISP | Threshold de Alerta | Threshold Crítico |
|-----|---------------------|-------------------|
| Gmail | > 0.08% | > 0.3% |
| Outlook | > 0.1% | > 0.5% |
| Yahoo | > 0.1% | > 0.3% |

### Google Postmaster Tools

1. Registrar domínio em [postmaster.google.com](https://postmaster.google.com)
2. Verificar DNS (registro TXT)
3. Monitorar:
   - **Domain Reputation** (High/Medium/Low/Bad)
   - **IP Reputation**
   - **Spam Rate**
   - **Authentication**

---

## Inbox Testing

### Ferramentas Recomendadas

| Ferramenta | Função | Custo |
|-----------|--------|-------|
| **Litmus** | Preview em 100+ clientes | Pago |
| **Email on Acid** | Teste de renderização | Pago |
| **Mail Tester** | Score de spam | Gratuito |
| **MX Toolbox** | DNS/Blacklist check | Gratuito |
| **GlockApps** | Inbox placement | Freemium |
| **SFMC Litmus Integration** | Dentro do SFMC | Add-on |

### Checklist antes de Envio

```
✅ SPF, DKIM e DMARC configurados e verificados
✅ Teste em Gmail, Outlook, Yahoo, Apple Mail
✅ Modo escuro testado
✅ Versão mobile testada (iOS + Android)
✅ Score no Mail Tester > 8/10
✅ Não está em blacklist (MX Toolbox)
✅ Ratio texto/imagem adequado (≥ 40% texto)
✅ Link de unsubscribe presente e funcional
✅ Preheader configurado
✅ From Name e From Email consistentes
```

---

## Conteúdo e Spam Filters

### Palavras que Ativam Filtros de Spam

```
❌ EVITAR: "Grátis", "FREE", "Ganhe agora", "Clique aqui",
            "Oferta imperdível", "100% grátis", "Sem risco",
            "Ganhe dinheiro", "!!!!", "URGENT", "Act NOW"

✅ PREFERIR: Linguagem natural, personalizada e relevante
             CTAs específicos: "Ver coleção", "Agendar visita"
             Assuntos concisos e descritivos
```

### Ratio Texto/Imagem

```
✅ Ideal: 60% texto, 40% imagem
❌ Evitar: e-mails 100% imagem (filtros bloqueiam)
✅ Sempre: Texto alternativo (alt text) em todas as imagens
```

---

## Métricas de Deliverability

| Métrica | Fórmula | Benchmark |
|---------|---------|-----------|
| **Delivery Rate** | Enviados - Bounces / Enviados | > 98% |
| **Bounce Rate** | Bounces / Enviados | < 2% |
| **Spam Complaint Rate** | Complaints / Enviados | < 0.1% |
| **Open Rate** | Abertos / Entregues | > 20% |
| **Unsubscribe Rate** | Unsubs / Enviados | < 0.5% |

---

## Referências

- [SFMC Deliverability](https://help.salesforce.com/s/articleView?id=sf.mc_es_deliverability_overview.htm)
- [Google Postmaster Tools](https://postmaster.google.com)
- [Mail Tester](https://www.mail-tester.com)
- [MX Toolbox](https://mxtoolbox.com)
- [Return Path / Validity](https://www.validity.com)
